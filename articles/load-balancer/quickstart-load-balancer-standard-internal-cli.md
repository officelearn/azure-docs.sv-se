---
title: 'Snabb start: skapa en intern belastningsutjämnare – Azure CLI'
titleSuffix: Azure Load Balancer
description: Den här snabb starten visar hur du skapar en intern belastningsutjämnare med hjälp av Azure CLI
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internal traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/23/2020
ms.author: allensu
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 834b5c3651a7fff085dc53096f66d5e3f4bf27b4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700428"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-using-azure-cli"></a>Snabb start: skapa en intern belastningsutjämnare för att belastningsutjämna virtuella datorer med Azure CLI

Kom igång med Azure Load Balancer med hjälp av Azure CLI och skapa en offentlig belastningsutjämnare och tre virtuella datorer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)] 

- Den här snabb starten kräver version 2.0.28 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

Skapa en resurs grupp med [AZ Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create):

* Med namnet **CreateIntLBQS-RG**. 
* På den **östra** platsen.

```azurecli-interactive
  az group create \
    --name CreateIntLBQS-rg \
    --location eastus
```
---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och distribuerar belastningsutjämnaren skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Med namnet **myVNet**.
* Adressprefix för **10.1.0.0/16**.
* Undernät med namnet **myBackendSubnet**.
* Undernätsprefixet för **10.1.0.0/24**.
* I resurs gruppen **CreateIntLBQS-RG** .
* Plats för **öster**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt** åtkomst.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* Nätverks gränssnitt för backend-servrarna.
* En moln konfigurations fil med namnet **cloud-init.txt** för Server konfigurationen.
* Två virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa två nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Med namnet **myNicVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Med namnet **myNicVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

### <a name="create-cloud-init-configuration-file"></a>Skapa konfigurations fil för Cloud-Init

Använd en konfigurations fil för Cloud-Init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. 

I ditt nuvarande gränssnitt skapar du en fil med namnet cloud-init.txt. Kopiera och klistra in följande konfiguration i gränssnittet. Se till att du kopierar hela Cloud-Init-filen korrekt, särskilt den första raden:

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
### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [AZ VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **UbuntuLTS** för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 1**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 1 \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Med namnet **myVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **UbuntuLTS** för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --zone 2 \
    --no-wait
```

Det kan ta några minuter innan de virtuella datorerna distribueras.

## <a name="create-standard-load-balancer"></a>Skapa standard Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Associerat med den virtuella nätverks **myVNet**.
* Kopplad till backend-undernätet **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Standard \
    --vnet-name myVnet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.
* Aktivera TCP-återställning.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --disable-outbound-snat true \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
>[!NOTE]
>De virtuella datorerna i backend-poolen har ingen utgående Internet anslutning med den här konfigurationen. </br> Mer information om hur du tillhandahåller utgående anslutningar finns i: </br> **[Utgående anslutningar i Azure](load-balancer-outbound-connections.md)**</br> Alternativ för att tillhandahålla anslutning: </br> **[Konfiguration för lastbalanserare med ”endast utgående”](egress-only.md)** </br> **[Vad är Virtual Network NAT?](../virtual-network/nat-overview.md)**

### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägg till virtuella datorer i backend-poolen för belastnings utjämning

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Associerad med nätverks gränssnittet **myNicVM1** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Associerad med nätverks gränssnittet **myNicVM2** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

# <a name="basic-sku"></a>[**Grundläggande SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Standard-SKU-belastningsutjämnare rekommenderas för produktions arbets belastningar. Mer information om SKU: er finns i **[Azure Load Balancer SKU: er](skus.md)**.

## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk

Innan du distribuerar virtuella datorer och distribuerar belastningsutjämnaren skapar du de stödda virtuella nätverks resurserna.

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Skapa ett virtuellt nätverk med [AZ Network VNet Create](/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-createt):

* Med namnet **myVNet**.
* Adressprefix för **10.1.0.0/16**.
* Undernät med namnet **myBackendSubnet**.
* Undernätsprefixet för **10.1.0.0/24**.
* I resurs gruppen **CreateIntLBQS-RG** .
* Plats för **öster**.

```azurecli-interactive
  az network vnet create \
    --resource-group CreateIntLBQS-rg \
    --location eastus \
    --name myVNet \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name myBackendSubnet \
    --subnet-prefixes 10.1.0.0/24
```
### <a name="create-a-network-security-group"></a>Skapa en nätverkssäkerhetsgrupp

För en standard belastningsutjämnare måste de virtuella datorerna i Server dels adressen ha nätverks gränssnitt som tillhör en nätverks säkerhets grupp. 

Skapa en nätverks säkerhets grupp med [AZ Network NSG Create](/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create):

* Med namnet **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.

```azurecli-interactive
  az network nsg create \
    --resource-group CreateIntLBQS-rg \
    --name myNSG
```

### <a name="create-a-network-security-group-rule"></a>Skapa en regel för nätverkssäkerhetsgruppen

Skapa en regel för nätverks säkerhets grupp med [AZ Network NSG Rule Create](/cli/azure/network/nsg/rule?view=azure-cli-latest#az-network-nsg-rule-create):

* Med namnet **myNSGRuleHTTP**.
* I den nätverks säkerhets grupp som du skapade i föregående steg, **myNSG**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Protokoll **(*)**.
* Riktningen **inkommande**.
* Källa **(*)**.
* Mål **(*)**.
* Målport- **port 80**.
* **Tillåt** åtkomst.
* Prioritet **200**.

```azurecli-interactive
  az network nsg rule create \
    --resource-group CreateIntLBQS-rg \
    --nsg-name myNSG \
    --name myNSGRuleHTTP \
    --protocol '*' \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```

### <a name="create-network-interfaces-for-the-virtual-machines"></a>Skapa nätverks gränssnitt för virtuella datorer

Skapa två nätverks gränssnitt med [AZ Network NIC Create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

#### <a name="vm1"></a>VM1

* Med namnet **myNicVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

```azurecli-interactive

  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM1 \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
#### <a name="vm2"></a>VM2

* Med namnet **myNicVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```

## <a name="create-backend-servers"></a>Skapa serverdelsservrar

I det här avsnittet skapar du:

* En moln konfigurations fil med namnet **cloud-init.txt** för Server konfigurationen. 
* Tillgänglighets uppsättning för de virtuella datorerna
* Två virtuella datorer som ska användas som backend-servrar för belastningsutjämnaren.

För att verifiera att belastningsutjämnaren har skapats installerar du NGINX på de virtuella datorerna.

### <a name="create-cloud-init-configuration-file"></a>Skapa konfigurations fil för Cloud-Init

Använd en konfigurations fil för Cloud-Init för att installera NGINX och köra en Hello World Node.js-app på en virtuell Linux-dator. 

I ditt nuvarande gränssnitt skapar du en fil med namnet cloud-init.txt. Kopiera och klistra in följande konfiguration i gränssnittet. Se till att du kopierar hela Cloud-Init-filen korrekt, särskilt den första raden:

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

### <a name="create-availability-set-for-virtual-machines"></a>Skapa tillgänglighets uppsättning för virtuella datorer

Skapa tillgänglighets uppsättningen med [AZ VM Availability-set Create](/cli/azure/vm/availability-set?view=azure-cli-latest#az-vm-availability-set-create):

* Med namnet **myAvSet**.
* I resurs gruppen **CreateIntLBQS-RG**.
* **Östra** platser.

```azurecli-interactive
  az vm availability-set create \
    --name myAvSet \
    --resource-group CreateIntLBQS-rg \
    --location eastus 
    
```

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa de virtuella datorerna med [AZ VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

#### <a name="vm1"></a>VM1
* Med namnet **myVM1**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM1**.
* **UbuntuLTS** för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I tillgänglighets uppsättningens **myAvSet**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM1 \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet \
    --no-wait
    
```
#### <a name="vm2"></a>VM2
* Med namnet **myVM2**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicVM2**.
* **UbuntuLTS** för avbildning av virtuell dator.
* Konfigurations filen **cloud-init.txt** du skapade i steg ovan.
* I **zon 2**.

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myVM2 \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --admin-user azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --availability-set myAvSet  \
    --no-wait
```

Det kan ta några minuter innan de virtuella datorerna distribueras.

## <a name="create-basic-load-balancer"></a>Skapa Basic Load Balancer

I det här avsnittet beskrivs hur du gör för att skapa och konfigurera följande komponenter i lastbalanseraren:

  * En IP-pool för klient delen som tar emot inkommande nätverks trafik i belastningsutjämnaren.
  * En server dels-IP-pool där frontend-poolen skickar den belastningsutjämnade nätverks trafiken.
  * En hälso avsökning som avgör hälso tillståndet för VM-instanser i Server delen.
  * En belastnings Utjämnings regel som definierar hur trafiken distribueras till de virtuella datorerna.

### <a name="create-the-load-balancer-resource"></a>Skapa belastnings Utjämnings resursen

Skapa en offentlig belastningsutjämnare med [AZ Network lb Create](/cli/azure/network/lb?view=azure-cli-latest#az-network-lb-create):

* Med namnet **myLoadBalancer**.
* En frontend-pool med namnet ' **frontend**'.
* En backend-pool med namnet **myBackEndPool**.
* Associerat med den virtuella nätverks **myVNet**.
* Kopplad till backend-undernätet **myBackendSubnet**.

```azurecli-interactive
  az network lb create \
    --resource-group CreateIntLBQS-rg \
    --name myLoadBalancer \
    --sku Basic \
    --vnet-name myVNet \
    --subnet myBackendSubnet \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
```

### <a name="create-the-health-probe"></a>Skapar hälsoavsökningen

En hälso avsökning kontrollerar alla virtuella dator instanser för att säkerställa att de kan skicka nätverks trafik. 

En virtuell dator med en misslyckad avsöknings kontroll har tagits bort från belastningsutjämnaren. Den virtuella datorn läggs tillbaka i belastningsutjämnaren när problemet är löst.

Skapa en hälso avsökning med [AZ Network lb PROBE Create](/cli/azure/network/lb/probe?view=azure-cli-latest#az-network-lb-probe-create):

* Övervakar hälso tillståndet för de virtuella datorerna.
* Med namnet **myHealthProbe**.
* Protokoll- **TCP**.
* Övervaknings **Port 80**.

```azurecli-interactive
  az network lb probe create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>Skapa lastbalanseringsregeln

En belastnings Utjämnings regel definierar:

* IP-konfiguration för klient delen för inkommande trafik.
* Server delens IP-pool för att ta emot trafiken.
* Käll-och mål port som krävs. 

Skapa en belastnings Utjämnings regel med [AZ Network lb Rule Create](/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create):

* Med namnet **myHTTPRule**
* Lyssnar på **Port 80** i frontend **-poolen för klient delen.**
* Skickar belastningsutjämnad nätverks trafik till Server dels adresspoolen **myBackEndPool** med **port 80**. 
* Använda **myHealthProbe** för hälso avsökning.
* Protokoll- **TCP**.
* Tids gräns för inaktivitet på **15 minuter**.

```azurecli-interactive
  az network lb rule create \
    --resource-group CreateIntLBQS-rg \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe \
    --idle-timeout 15 
```
### <a name="add-virtual-machines-to-load-balancer-backend-pool"></a>Lägg till virtuella datorer i backend-poolen för belastnings utjämning

Lägg till de virtuella datorerna i backend-poolen med [AZ Network NIC IP-config Address-pool Add](/cli/azure/network/nic/ip-config/address-pool?view=azure-cli-latest#az-network-nic-ip-config-address-pool-add):


#### <a name="vm1"></a>VM1
* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Associerad med nätverks gränssnittet **myNicVM1** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM1 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

#### <a name="vm2"></a>VM2
* I backend-adresspoolen **myBackEndPool**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Associerad med nätverks gränssnittet **myNicVM2** och **ipconfig1**.
* Kopplad till belastningsutjämnare **myLoadBalancer**.

```azurecli-interactive
  az network nic ip-config address-pool add \
   --address-pool myBackendPool \
   --ip-config-name ipconfig1 \
   --nic-name myNicVM2 \
   --resource-group CreateIntLBQS-rg \
   --lb-name myLoadBalancer
```

---

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

### <a name="create-azure-bastion-public-ip"></a>Skapa Azure skydds offentlig IP

Använd [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) för att skapa en offentlig IP-adress för skydds-värden:

* Skapa en standard zon för redundant offentlig IP-adress med namnet **myBastionIP**.
* I **CreateIntLBQS-RG**.

```azurecli-interactive
  az network public-ip create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionIP \
    --sku Standard
```

### <a name="create-azure-bastion-subnet"></a>Skapa ett Azure skydds-undernät

Använd [AZ Network VNet Subnet Create](/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-create) för att skapa ett undernät:

* Med namnet **AzureBastionSubnet**.
* Adressprefix för **10.1.1.0/24**.
* I virtuellt nätverk **myVNet**.
* I resurs gruppen **CreateIntLBQS-RG**.

```azurecli-interactive
  az network vnet subnet create \
    --resource-group CreateIntLBQS-rg \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

### <a name="create-azure-bastion-host"></a>Skapa en Azure skydds-värd
Använd [AZ Network skydds Create](/cli/azure/network/bastion?view=azure-cli-latest#az-network-bastion-create) för att skapa en skydds-värd:

* Med namnet **myBastionHost**
* I **CreateIntLBQS-RG**
* Associerat med offentliga IP- **myBastionIP**.
* Associerad med **myVNet** för virtuellt nätverk.
* På **östra** platsen.

```azurecli-interactive
  az network bastion create \
    --resource-group CreateIntLBQS-rg \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus
```
Det tar några minuter för skydds-värden att distribueras.

### <a name="create-test-virtual-machine"></a>Skapa virtuell test dator

Skapa nätverks gränssnittet med [AZ Network NIC Create](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create):

* Med namnet **myNicTestVM**.
* I resurs gruppen **CreateIntLBQS-RG**.
* I virtuellt nätverk **myVNet**.
* I undernät **myBackendSubnet**.
* I nätverks säkerhets gruppen **myNSG**.

```azurecli-interactive
  az network nic create \
    --resource-group CreateIntLBQS-rg \
    --name myNicTestVM \
    --vnet-name myVNet \
    --subnet myBackEndSubnet \
    --network-security-group myNSG
```
Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm?view=azure-cli-latest#az-vm-create):

* Med namnet **myTestVM**.
* I resurs gruppen **CreateIntLBQS-RG**.
* Ansluten till nätverks gränssnittet **myNicTestVM**.
* **Win2019Datacenter** för avbildning av virtuell dator.
* Välj värden för **\<adminpass>** och **\<adminuser>** .
  

```azurecli-interactive
  az vm create \
    --resource-group CreateIntLBQS-rg \
    --name myTestVM \
    --nics myNicTestVM \
    --image Win2019Datacenter \
    --admin-username <adminuser> \
    --admin-password <adminpass> \
    --no-wait
```
Det kan ta några minuter att distribuera den virtuella datorn.

### <a name="test"></a>Testa

1. [Logga in](https://portal.azure.com) i Azure-portalen.

1. Hitta den privata IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer**.

2. Anteckna eller kopiera adressen bredvid **privat IP-adress** i **översikten** över **myLoadBalancer**.

3. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj **myTestVM** i resurs gruppen **CreateIntLBQS-RG** i resurs listan.

4. På sidan **Översikt** väljer du **Anslut** och sedan **skydds**.

6. Ange det användar namn och lösen ord som angavs när den virtuella datorn skapades.

7. Öppna **Internet Explorer** på **myTestVM**.

8. Ange IP-adressen från föregående steg i adress fältet i webbläsaren. IIS-webbserverns standardsida visas i webbläsaren.

    :::image type="content" source="./media/quickstart-load-balancer-standard-internal-portal/load-balancer-test.png" alt-text="Skapa en intern standard belastnings utjämning" border="true":::
   
Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer kan du anpassa standard sidan för varje virtuell dators IIS-webbserver och sedan framtvinga en uppdatering av webbläsaren från klient datorn.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) för att ta bort resurs gruppen, belastningsutjämnaren och alla relaterade resurser.

```azurecli-interactive
  az group delete \
    --name CreateIntLBQS-rg
```

## <a name="next-steps"></a>Nästa steg
I den här snabb starten

* Du har skapat en standard-eller offentlig belastningsutjämnare
* Anslutna virtuella datorer. 
* Konfigurerat trafik regel för belastnings utjämning och hälso avsökning.
* Belastnings utjämning har testats.

Om du vill veta mer om Azure Load Balancer fortsätter du till 
> [!div class="nextstepaction"]
> [Vad är Azure Load Balancer?](load-balancer-overview.md)