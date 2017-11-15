---
title: "Hur du läser in balansera Linux virtuella datorer i Azure | Microsoft Docs"
description: "Lär dig hur du skapar ett program med hög tillgänglighet och säkert över tre virtuella Linux-datorer med hjälp av Azure belastningsutjämnare"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/13/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dc25d6106ad67710660b1a5c48270a7082688d51
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2017
---
# <a name="how-to-load-balance-linux-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Hur du läser in balansera Linux-datorer i Azure för att skapa ett program med hög tillgänglighet
Belastningsutjämning ger högre tillgänglighet genom att sprida inkommande begäranden över flera virtuella datorer. I kursen får du lära dig om de olika komponenterna i Azure belastningsutjämnare som distribuerar trafik och ger hög tillgänglighet. Lär dig att:

> [!div class="checklist"]
> * Skapa en Azure belastningsutjämnare
> * Skapa en belastningsutjämnaren, hälsoavsökningen
> * Skapa regler för nätverkstrafik för belastningsutjämnare
> * Använda molntjänster init för att skapa en grundläggande Node.js-app
> * Skapa virtuella datorer och ansluta till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="azure-load-balancer-overview"></a>Översikt över Azure belastningen belastningsutjämnare
En Azure belastningsutjämnare är en belastningsutjämnare för Layer-4 (TCP, UDP) som ger hög tillgänglighet genom att distribuera inkommande trafik mellan felfri virtuella datorer. En belastningsutjämnaren, hälsoavsökningen övervakar en viss port på varje virtuell dator och distribuerar endast trafik till en virtuell dator i drift.

Du kan definiera en frontend IP-konfiguration som innehåller en eller flera offentliga IP-adresser. Den här frontend IP-konfigurationen kan din belastningsutjämnare och program som ska vara tillgänglig via Internet. 

Virtuella datorer ansluta till en belastningsutjämnare med hjälp av deras virtuella nätverksgränssnittskortet (NIC). Om du vill distribuera trafik till de virtuella datorerna en backend-adresspool som innehåller IP-adresser för virtuella (NIC) som är anslutna till belastningsutjämnaren.

Om du vill styra flödet av trafik, kan du definiera belastningen belastningsutjämnaren regler för specifika portar och protokoll som mappar till dina virtuella datorer.

Om du har följt tidigare guiden för att [skapa en virtuella datorns skaluppsättning](tutorial-create-vmss.md), belastningsutjämning skapades för dig. Alla dessa komponenter konfigurerades för dig som en del av skaluppsättning.


## <a name="create-azure-load-balancer"></a>Skapa Azure belastningsutjämnare
Det här avsnittet beskrivs hur du kan skapa och konfigurera varje komponent i belastningsutjämnaren. Innan du kan skapa din belastningsutjämnare, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupLoadBalancer* i den *eastus* plats:

```azurecli-interactive 
az group create --name myResourceGroupLoadBalancer --location eastus
```

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress
För att komma åt din app på Internet, måste en offentlig IP-adress för belastningsutjämnaren. Skapa en offentlig IP-adress med [az nätverket offentliga IP-skapa](/cli/azure/network/public-ip#create). I följande exempel skapas en offentlig IP-adress med namnet *myPublicIP* i den *myResourceGroupLoadBalancer* resursgrupp:

```azurecli-interactive 
az network public-ip create \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP
```

### <a name="create-a-load-balancer"></a>Skapa en belastningsutjämnare
Skapa en belastningsutjämnare med [az nätverket lb skapa](/cli/azure/network/lb#create). I följande exempel skapas en belastningsutjämnare med namnet *myLoadBalancer* och tilldelar den *myPublicIP* adressen till frontend IP-konfigurationen:

```azurecli-interactive 
az network lb create \
    --resource-group myResourceGroupLoadBalancer \
    --name myLoadBalancer \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --public-ip-address myPublicIP
```

### <a name="create-a-health-probe"></a>Skapa en hälsoavsökningen
Om du vill att belastningsutjämnaren ska övervaka status för din app kan du använda en hälsoavsökningen. Avsökningen hälsa dynamiskt lägger till eller tar bort virtuella datorer från belastningen belastningsutjämnaren rotationen baserat på deras svar på hälsokontroller. En virtuell dator tas bort från belastningsutjämnaren belastningsdistribution efter två på varandra följande fel intervaller 15 sekunder som standard. Du skapar en hälsoavsökningen baserat på ett protokoll eller ett visst hälsotillstånd sidan för kontroll för din app. 

I följande exempel skapas en TCP-avsökning. Du kan också skapa anpassade HTTP-avsökningar mer detaljerade hälsokontroller. När du använder en anpassad HTTP-avsökningen, måste du skapa sidan för kontroll av hälsotillstånd som *healthcheck.js*. Avsökningen måste returnera ett **HTTP 200 OK** svar att behålla värden i rotation belastningsutjämnaren.

Om du vill skapa en TCP-hälsoavsökningen du använder [az nätverket lb avsökningen skapa](/cli/azure/network/lb/probe#create). I följande exempel skapas en hälsoavsökningen med namnet *myHealthProbe*:

```azurecli-interactive 
az network lb probe create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80
```

### <a name="create-a-load-balancer-rule"></a>Skapa en regel för belastningsutjämnare
En regel för belastningsutjämnare används för att definiera hur trafiken distribueras till de virtuella datorerna. Du kan definiera frontend IP-konfiguration för inkommande trafik och backend-IP-adresspool för att ta emot trafik, tillsammans med nödvändig käll- och port. Om du vill kontrollera endast felfri virtuella datorer ta emot trafik kan definiera du också hälsoavsökningen att använda.

Skapa en regel för belastningsutjämnare med [az nätverket lb regeln skapa](/cli/azure/network/lb/rule#create). I följande exempel skapas en regel med namnet *myLoadBalancerRule*, använder den *myHealthProbe* hälsoavsökningen och saldon trafik på port *80*:

```azurecli-interactive 
az network lb rule create \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe
```


## <a name="configure-virtual-network"></a>Konfigurera ett virtuellt nätverk
Innan du distribuerar vissa virtuella datorer och testa din belastningsutjämnare, skapa stödresurser för virtuellt nätverk. Mer information om virtuella nätverk finns i [hantera virtuella Azure-nätverk](tutorial-virtual-network.md) kursen.

### <a name="create-network-resources"></a>Skapa nätverksresurser
Skapa ett virtuellt nätverk med [az network vnet skapa](/cli/azure/network/vnet#create). I följande exempel skapas ett virtuellt nätverk med namnet *myVnet* med ett undernät med namnet *mySubnet*:

```azurecli-interactive 
az network vnet create \
    --resource-group myResourceGroupLoadBalancer \
    --name myVnet \
    --subnet-name mySubnet
```

Om du vill lägga till en nätverkssäkerhetsgrupp måste du använda [az nätverket nsg skapa](/cli/azure/network/nsg#create). I följande exempel skapas en nätverkssäkerhetsgrupp med namnet *myNetworkSecurityGroup*:

```azurecli-interactive 
az network nsg create \
    --resource-group myResourceGroupLoadBalancer \
    --name myNetworkSecurityGroup
```

Skapa en grupp nätverkssäkerhetsregeln med [az nätverket nsg regeln skapa](/cli/azure/network/nsg/rule#create). I följande exempel skapas en grupp nätverkssäkerhetsregeln med namnet *myNetworkSecurityGroupRule*:

```azurecli-interactive 
az network nsg rule create \
    --resource-group myResourceGroupLoadBalancer \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --priority 1001 \
    --protocol tcp \
    --destination-port-range 80
```

Virtuella nätverkskort skapas med [az nätverket nic skapa](/cli/azure/network/nic#create). I följande exempel skapas tre virtuella nätverkskort. (Ett virtuellt nätverkskort för varje virtuell dator skapar du en app i följande steg). Du kan skapa ytterligare virtuella nätverkskort och virtuella datorer när som helst och lägga till dem i belastningsutjämnaren:

```bash
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```

När alla tre virtuella nätverkskort skapas, Fortsätt till nästa steg


## <a name="create-virtual-machines"></a>Skapa virtuella datorer

### <a name="create-cloud-init-config"></a>Skapa moln init config
I en tidigare självstudiekurs om [hur du anpassar en Linux-dator vid den första starten](tutorial-automate-vm-deployment.md), du lärt dig hur du automatiserar VM anpassning med molnet initiering. Du kan använda samma molnet init-konfigurationsfilen för att installera NGINX och köra en enkel ”Hello World” Node.js-app i nästa steg. Åtkomst till den här enkla appen i en webbläsare om du vill se belastningsutjämnaren i åtgärden, i slutet av kursen.

Skapa en fil med namnet i din aktuella shell *moln init.txt* och klistra in följande konfiguration. Till exempel skapa filen i molnet Shell inte på den lokala datorn. Ange `sensible-editor cloud-init.txt` att skapa filen och se en lista över tillgängliga redigerare. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

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
Placera dina virtuella datorer i en tillgänglighetsuppsättning för att förbättra tillgängligheten för din app. Mer information om tillgänglighetsuppsättningar finns i den tidigare [hur du skapar virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md) kursen.

Skapa en tillgänglighetsuppsättning med [az vm tillgänglighetsuppsättning skapa](/cli/azure/vm/availability-set#create). I följande exempel skapas en tillgänglighetsuppsättning namngivna *myAvailabilitySet*:

```azurecli-interactive 
az vm availability-set create \
    --resource-group myResourceGroupLoadBalancer \
    --name myAvailabilitySet
```

Nu kan du skapa de virtuella datorerna med [az vm skapa](/cli/azure/vm#create). I följande exempel skapas tre virtuella datorer och genererar SSH-nycklar, om de inte redan finns:

```bash
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```

Det finns bakgrundsaktiviteter för att fortsätta att köras när Azure CLI återgår till Kommandotolken. Den `--no-wait` parametern inte väntar på att alla aktiviteter slutförts. Det kan vara en annan några minuter innan du kan komma åt appen. I belastningsutjämnaren, hälsoavsökningen identifierar automatiskt när appen körs på varje virtuell dator. När appen körs börjar belastningsutjämningsregeln distribuera trafiken.


## <a name="test-load-balancer"></a>Testa belastningsutjämnare
Hämta offentlig IP-adressen för din belastningsutjämnare med [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#show). I följande exempel hämtar IP-adressen för *myPublicIP* skapade tidigare:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupLoadBalancer \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
```

Du kan sedan ange den offentliga IP-adressen i en webbläsare. Kom ihåg - det tar några minuter för de virtuella datorerna ska bli klar innan belastningsutjämnaren börjar distribuera trafiken till dem. Appen visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till som i följande exempel:

![Node.js-app som körs](./media/tutorial-load-balancer/running-nodejs-app.png)

Om du vill se belastningsutjämnaren distribuerar trafik över alla tre virtuella datorer som kör appen du kan force-uppdatera webbläsaren.


## <a name="add-and-remove-vms"></a>Lägga till och ta bort virtuella datorer
Du kan behöva utföra underhåll på virtuella datorer som kör appen, till exempel installera uppdateringar av OS. Du kan behöva lägga till ytterligare virtuella datorer för att hantera den ökade trafiken till din app. Det här avsnittet visar hur du tar bort eller lägga till en virtuell dator från belastningsutjämnaren.

### <a name="remove-a-vm-from-the-load-balancer"></a>Ta bort en virtuell dator från belastningsutjämnaren
Du kan ta bort en virtuell dator från backend-adresspool med [az nic ip-config-nätverksadresspool ta bort](/cli/azure/network/nic/ip-config/address-pool#remove). I följande exempel tar bort det virtuella nätverkskortet för **myVM2** från *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool remove \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool 
```

Om du vill se belastningsutjämnaren distribuerar trafik över de återstående två virtuella datorerna kör appen du kan framtvinga-uppdatera webbläsaren. Du kan nu utföra underhåll på den virtuella datorn, till exempel installera uppdateringar av operativsystem eller utföra en VM-omstart.

Du kan visa en lista över virtuella datorer med virtuella nätverkskort som är anslutna till belastningsutjämnaren [az nätverket lb adresspool visa](/cli/azure/network/lb/address-pool#show). Fråga efter och filtrera efter ID för det virtuella nätverkskortet på följande sätt:

```azurecli-interactive
az network lb address-pool show \
    --resource-group myResourceGroupLoadBalancer \
    --lb-name myLoadBalancer \
    --name myBackEndPool \
    --query backendIpConfigurations \
    --output tsv | cut -f4
```

Utdata liknar följande exempel som visar att det virtuella nätverkskortet för VM 2 inte längre är en del av backend-adresspool:

```bash
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic1/ipConfigurations/ipconfig1
/subscriptions/<guid>/resourceGroups/myResourceGroupLoadBalancer/providers/Microsoft.Network/networkInterfaces/myNic3/ipConfigurations/ipconfig1
```

### <a name="add-a-vm-to-the-load-balancer"></a>Lägga till en virtuell dator till belastningsutjämnaren
När du utför underhåll på VM, eller om du behöver Utöka kapaciteten kan du lägga till en virtuell dator till backend-adresspool med [az nic ip-config-nätverksadresspool lägga till](/cli/azure/network/nic/ip-config/address-pool#add). I följande exempel läggs det virtuella nätverkskortet för **myVM2** till *myLoadBalancer*:

```azurecli-interactive 
az network nic ip-config address-pool add \
    --resource-group myResourceGroupLoadBalancer \
    --nic-name myNic2 \
    --ip-config-name ipConfig1 \
    --lb-name myLoadBalancer \
    --address-pool myBackEndPool
```

Använd för att kontrollera att det virtuella nätverkskortet är anslutet till backend-adresspool [az nätverket lb adresspool visa](/cli/azure/network/lb/address-pool#show) igen från föregående steg.


## <a name="next-steps"></a>Nästa steg
I kursen får du skapat en belastningsutjämnare och anslutna virtuella datorer. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en Azure belastningsutjämnare
> * Skapa en belastningsutjämnaren, hälsoavsökningen
> * Skapa regler för nätverkstrafik för belastningsutjämnare
> * Använda molntjänster init för att skapa en grundläggande Node.js-app
> * Skapa virtuella datorer och ansluta till en belastningsutjämnare
> * Visa en belastningsutjämnare i praktiken
> * Lägga till och ta bort virtuella datorer från en belastningsutjämnare

Gå vidare till nästa kurs lära dig mer om virtuella Azure-nätverkskomponenter.

> [!div class="nextstepaction"]
> [Hantera virtuella datorer och virtuella nätverk](tutorial-virtual-network.md)
