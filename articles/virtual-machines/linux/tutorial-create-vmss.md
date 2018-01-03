---
title: "Skapa en Skalningsuppsättningar i virtuella datorer för Linux i Azure | Microsoft Docs"
description: "Skapa och distribuera ett program som har hög tillgänglighet på virtuella Linux-datorer med hjälp av en skaluppsättning för virtuell dator"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 12/15/2017
ms.author: iainfou
ms.openlocfilehash: 04fe3948f37936b43d1f2155635f0f52583d5e1b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Skapa en Virtual Machine Scale Set och distribuera en app som har hög tillgänglighet på Linux
En skaluppsättning för virtuell dator kan du distribuera och hantera en uppsättning identiska, automatisk skalning virtuella datorer. Du kan skala antalet virtuella datorer i skaluppsättning manuellt eller definiera regler för att kunna Autoskala baserat på Resursanvändning t.ex CPU, minne begäran eller nätverkstrafik. I kursen får distribuera du en virtuell dator skala i Azure. Lär dig att:

> [!div class="checklist"]
> * Använda molntjänster init för att skapa en app att skala
> * Skapa en skaluppsättning för virtuell dator
> * Öka eller minska antalet instanser i en skaluppsättning
> * Skapa automatiska regler
> * Visa anslutningsinformation för scale set-instanser
> * Använda datadiskar i en skaluppsättning


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.22 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Skala Set-översikt
En skaluppsättning för virtuell dator kan du distribuera och hantera en uppsättning identiska, automatisk skalning virtuella datorer. Virtuella datorer i en skaluppsättning är fördelade på logiken fel- och update-domäner i en eller flera *placering grupper*. Dessa är grupper med liknande konfigurerad virtuella datorer, liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skaluppsättning. Du kan definiera automatiska regler för att styra hur och när virtuella datorer läggs till eller tas bort från skaluppsättning. De här reglerna kan utlösa baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

Skala anger stöd för upp till 1 000 virtuella datorer när du använder en avbildning i Azure-plattformen. För arbetsbelastningar med betydande installation eller VM anpassning krav, kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md). Du kan skapa upp till 300 virtuella datorer i en skala som anges när du använder en anpassad avbildning.


## <a name="create-an-app-to-scale"></a>Skapa en app att skala
För produktion, kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md) som innehåller programmet installeras och konfigureras. Den här kursen kan du anpassa de virtuella datorerna på startas för första gången du snabbt vill se en skala som anges i åtgärden.

I en tidigare kursen du lärt dig [hur du anpassar en Linux-dator vid den första starten](tutorial-automate-vm-deployment.md) med molnet initiering. Du kan använda samma molnet init-konfigurationsfilen för att installera NGINX och köra en enkel ”Hello World” Node.js-app. 

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


## <a name="create-a-scale-set"></a>Skapa en skaluppsättning
Innan du kan skapa en skalningsuppsättning, skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupScaleSet* i den *eastus* plats:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Nu skapa en virtuell dator-skala med [az vmss skapa](/cli/azure/vmss#create). I följande exempel skapas en uppsättning med namnet skala *myScaleSet*använder molnet init-filen för att anpassa den virtuella datorn och genererar SSH-nycklar, om de inte finns:

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skala uppsättning resurser och virtuella datorer. Det finns bakgrundsaktiviteter för att fortsätta att köras när Azure CLI återgår till Kommandotolken. Det kan vara en annan några minuter innan du kan komma åt appen.


## <a name="allow-web-traffic"></a>Tillåt webbtrafik
En belastningsutjämnare har skapats automatiskt som en del av virtuella datorns skaluppsättning. Belastningsutjämnaren distribuerar trafik över en uppsättning definierade virtuella datorer med hjälp av regler för inläsning av belastningsutjämnaren. Du kan lära dig mer om belastningen belastningsutjämnaren koncept och konfigurationen i nästa kurs [så att belastningsutjämna virtuella datorer i Azure](tutorial-load-balancer.md).

Skapa en regel med för att tillåta trafik till webbappen [az nätverket lb regeln skapa](/cli/azure/network/lb/rule#create). I följande exempel skapas en regel med namnet *myLoadBalancerRuleWeb*:

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>Testa din app
Om du vill se din Node.js-app på webben, hämta offentlig IP-adressen för din belastningsutjämnare med [az nätverket offentliga ip-visa](/cli/azure/network/public-ip#show). I följande exempel hämtar IP-adressen för *myScaleSetLBPublicIP* skapas som en del av skaluppsättning:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Ange den offentliga IP-adressen i en webbläsare. Appen visas, inklusive värdnamnet för den virtuella datorn som belastningsutjämnaren distribuerade trafik till:

![Node.js-app som körs](./media/tutorial-create-vmss/running-nodejs-app.png)

Om du vill se skaluppsättningen i praktiken du kan framtvinga-uppdatera webbläsaren om du vill se belastningsutjämnaren distribuerar trafik över alla de virtuella datorerna kör appen.


## <a name="management-tasks"></a>Administrativa uppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skaluppsättning. Dessutom kanske du vill skapa skript som automatiserar olika livscykel-uppgifter. Azure CLI 2.0 tillhandahåller ett snabbt sätt att utföra dessa uppgifter. Här följer några vanliga uppgifter.

### <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skaluppsättning
Du kan visa en lista över virtuella datorer som körs i en skaluppsättning [az vmss listinstanserna](/cli/azure/vmss#list-instances) på följande sätt:

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

Utdata ser ut ungefär så här:

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>Öka eller minska VM-instanser
Om du vill se antalet instanser som du har för närvarande i en skaluppsättning [az vmss visa](/cli/azure/vmss#show) och fråga på *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan manuellt öka eller minska antalet virtuella datorer i skaluppsättningen med [az vmss skala](/cli/azure/vmss#scale). I följande exempel anger hur många virtuella datorer i din skaluppsättningen *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Konfigurera automatiska regler
Du kan definiera automatiska regler i stället för att skala antalet instanser manuellt i din skala har angetts. De här reglerna övervaka instanser i en skaluppsättning och svara därefter baserat på mått och tröskelvärden som du definierar. I följande exempel skalas ut antalet instanser av en när Genomsnittlig CPU-belastningen är större än 60% under en period på 5 minuter. Om Genomsnittlig CPU-belastningen sedan sjunker under 30% under en period på 5 minuter, skalas instanser i en instans. Prenumerations-ID används för att skapa resursen URI: er för olika scale set-komponenter. Så här skapar du de här reglerna med [az Autoskala-inställningar för övervakning av skapa](/cli/azure/monitor/autoscale-settings#create), kopiera och klistra in följande Autoskala kommandot profil:

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

Om du vill återanvända Autoskala profil, kan du skapa en JSON (JavaScript Object Notation)-fil och skicka som att den `az monitor autoscale-settings create` kommandot med de `--parameters @autoscale.json` parameter. Design av användningen av Autoskala, Läs mer [Autoskala metodtips](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Hämta anslutningsinformation
Så här skaffar du anslutningsinformationen om de virtuella datorerna i din skaluppsättningar [az vmss lista-instans--anslutningsinformation](/cli/azure/vmss#list-instance-connection-info). Detta kommando offentlig IP-adress och port för varje virtuell dator där du kan ansluta med SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Använda datadiskar med skaluppsättningar
Du kan skapa och använda datadiskar med skaluppsättningar. I en tidigare kursen du lärt dig hur du [hantera Azure-diskar](tutorial-manage-disks.md) som beskrivs bästa praxis och prestandaförbättringar för att skapa appar på datadiskar i stället för OS-disk.

### <a name="create-scale-set-with-data-disks"></a>Skapa skaluppsättning med datadiskar
Om du vill skapa en skalningsuppsättning och bifoga datadiskar, lägger du till den `--data-disk-sizes-gb` parametern till den [az vmss skapa](/cli/azure/vmss#create) kommando. I följande exempel skapas en skala med *50*Gb datadiskar kopplade till varje instans:

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

När instanser tas bort från en skaluppsättning för tas eventuella anslutna hårddiskar också bort.

### <a name="add-data-disks"></a>Lägg till datadiskar
Lägg till en datadisk till instanser i en skaluppsättning för att använda [az vmss disk bifoga](/cli/azure/vmss/disk#attach). I följande exempel läggs en *50*Gb disk till varje instans:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Koppla från datadiskar
Ta bort en datadisk till instanser i en skaluppsättning med [az vmss disk frånkoppling](/cli/azure/vmss/disk#detach). I följande exempel tar bort datadisk på LUN *2* från varje instans:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Nästa steg
Du har skapat en skaluppsättning för virtuell dator i den här självstudiekursen. Du har lärt dig att:

> [!div class="checklist"]
> * Använda molntjänster init för att skapa en app att skala
> * Skapa en skaluppsättning för virtuell dator
> * Öka eller minska antalet instanser i en skaluppsättning
> * Skapa automatiska regler
> * Visa anslutningsinformation för scale set-instanser
> * Använda datadiskar i en skaluppsättning

Gå vidare till nästa kurs att lära dig mer om koncept för virtuella datorer för belastningsutjämning.

> [!div class="nextstepaction"]
> [Belastningsutjämna virtuella datorer](tutorial-load-balancer.md)
