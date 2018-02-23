---
title: "Skapa en VM-skalningsuppsättning för Linux i Azure | Microsoft Docs"
description: "Skapa och distribuera ett program med hög tillgänglighet på virtuella Linux-datorer med hjälp av en VM-skalningsuppsättning"
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
ms.openlocfilehash: 263983017e08dcc9a8e614c159ef5afaaf1d924e
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>Skapa en VM-skalningsuppsättning och distribuera ett program med hög tillgänglighet på Linux
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Du kan skala antalet virtuella datorer i skalningsuppsättningen manuellt eller definiera regler för automatisk skalning baserat på resursanvändning, till exempel CPU, minneskrav eller nätverkstrafik. I självstudien distribuerar du en VM-skalningsuppsättning i Azure. Lär dig att:

> [!div class="checklist"]
> * Använda cloud-init för att skapa ett skalbart program
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning
> * Visa anslutningsinformation för skalningsuppsättningsinstanser
> * Använda datadiskar i en skalningsuppsättning


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.22 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="scale-set-overview"></a>Översikt över skalningsuppsättning
Med en VM-skalningsuppsättning kan du distribuera och hantera en uppsättning identiska, virtuella datorer med automatisk skalning. Virtuella datorer i en skalningsuppsättning är distribuerade över logiska fel- och uppdateringsdomäner i en eller flera *placeringsgrupper*. Detta är grupper med virtuella datorer som har en liknande konfiguration, vilket liknar [tillgänglighetsuppsättningar](tutorial-availability-sets.md).

Virtuella datorer skapas efter behov i en skalningsuppsättning. Du kan definiera regler för automatisk skalning om du vill styra hur och när virtuella datorer läggs till eller tas bort från skalningsuppsättningen. De här reglerna kan utlösas baserat på mått som CPU-belastning, minnesanvändning eller nätverkstrafik.

Skalningsuppsättningar har stöd för upp till 1 000 virtuella datorer när du använder en avbildning i Azure-plattformen. För arbetsbelastningar med större installations- eller VM-anpassningskrav kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md). Du kan skapa upp till 300 virtuella datorer i en skalningsuppsättning när du använder en anpassad avbildning.


## <a name="create-an-app-to-scale"></a>Skapa ett program som ska skalas
Vid produktion kan du [skapa en anpassad VM-avbildning](tutorial-custom-images.md) som innehåller det installerade och konfigurerade programmet. I den här självstudien anpassar vi de virtuella datorerna vid den första starten för att snabbt se när en skalningsuppsättning görs.

I en tidigare självstudie lärde du dig [hur du anpassar en virtuell Linux-dator vid den första starten](tutorial-automate-vm-deployment.md) med cloud-init. Du kan använda samma konfigurationsfil för cloud-init när du installerar NGINX och kör en enkel ”Hello World” Node.js-app. 

I ditt nuvarande gränssnitt skapar du en fil med namnet *cloud-init.txt* och klistrar in följande konfiguration. Skapa till exempel inte filen i Cloud Shell på din lokala dator. Ange `sensible-editor cloud-init.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden:

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


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Innan du kan skapa en skalningsuppsättning skapar du en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroupScaleSet* på platsen *eastus*:

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss#az_vmss_create). I följande exempel skapas en skalningsuppsättning med namnet *myScaleSet* som använder filen cloud-init till att anpassa den virtuella datorn och som genererar SSH-nycklar om de inte redan finns:

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

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer. Det finns bakgrundsaktiviteter som fortsätter köras när Azure CLI återgår till frågan. Det kan ta några minuter innan du kan öppna programmet.


## <a name="allow-web-traffic"></a>Tillåt webbtrafik
En belastningsutjämnare har skapats automatiskt som en del av den virtuella datorns skalningsuppsättning. Belastningsutjämnaren distribuerar trafik över en uppsättning definierade virtuella datorer med hjälp av regler för belastningsutjämnaren. Du kan lära dig mer om belastningsutjämnarens koncept och konfiguration i nästa självstudie [Så här utjämnar du belastningen för virtuella datorer i Azure](tutorial-load-balancer.md).

Skapa en regel med [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) för att tillåta trafik till webbappen. I följande exempel skapas en regel med namnet *myLoadBalancerRuleWeb*:

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
Om du vill se Node.js-appen hämtar du den offentliga IP-adressen för belastningsutjämnaren med [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

Ange den offentliga IP-adressen i en webbläsare. Programmet visas med värddatornamnet för den virtuella dator som belastningsutjämnaren distribuerade trafik till:

![Köra Node.js-app](./media/tutorial-create-vmss/running-nodejs-app.png)

Om du vill se när skalningsuppsättningen används kan du framtvinga en uppdatering av webbläsaren. Då visas hur belastningsutjämnaren distribuerar trafik över alla virtuella datorer som kör programmet.


## <a name="management-tasks"></a>Administrativa uppgifter
Du kan behöva köra en eller flera administrativa uppgifter i hela livscykeln för skalningsuppsättningen. Dessutom kanske du vill skapa skript som automatiserar olika livscykeluppgifter. Azure CLI 2.0 innehåller ett snabbt sätt att utföra dessa uppgifter på. Här följer några vanliga uppgifter.

### <a name="view-vms-in-a-scale-set"></a>Visa virtuella datorer i en skalningsuppsättning
Du kan visa en lista med de virtuella datorer som körs i din skalningsuppsättning med hjälp av [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) på följande sätt:

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
Om du vill se antalet instanser som du för närvarande har i en skalningsuppsättning använder du [az vmss show](/cli/azure/vmss#az_vmss_show) och frågar efter *sku.capacity*:

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

Du kan sedan manuellt öka eller minska antalet virtuella datorer i skalningsuppsättningen med [az vmss scale](/cli/azure/vmss#az_vmss_scale). I följande exempel anges antalet virtuella datorer i din skalningsuppsättning till *3*:

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 3
```


### <a name="configure-autoscale-rules"></a>Konfigurera regler för automatisk skalning
Du kan definiera regler för automatisk skalning i stället för att skala antalet instanser manuellt i skalningsuppsättningen. De här reglerna övervakar instanserna i skalningsuppsättningen och svarar därefter baserat på de mått och tröskelvärden som du definierar. I följande exempel skalas antalet instanser ut med en när den genomsnittliga CPU-belastningen är större än 60 % under en 5-minutersperiod. Om den genomsnittliga CPU-belastningen sedan sjunker under 30 % under en 5-minutersperiod, skalas instanserna in med en instans. Ditt prenumerations-ID används till att skapa resurs-URI:er för olika komponenter i skalningsuppsättningen. Skapa reglerna med [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) genom att kopiera och klistra in följande kommandoprofil för autoskalning:

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

Om du vill återanvända autoskalningsprofilen kan du skapa en JSON-fil (JavaScript Object Notation) och skicka den till `az monitor autoscale-settings create`-kommandot med `--parameters @autoscale.json`-parametern. Mer information om användningen av autoskalning finns i [Metodtips för autoskalning](/azure/architecture/best-practices/auto-scaling).


### <a name="get-connection-info"></a>Hämta anslutningsinformation
Om du vill hämta anslutningsinformation om de virtuella datorerna i dina skaluppsättningar använder du [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info). Detta kommando visar offentlig IP-adress och port för varje virtuell dator där du kan ansluta med SSH:

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>Använda datadiskar med skalningsuppsättningar
Du kan skapa och använda datadiskar med skalningsuppsättningar. I en tidigare självstudie lärde du dig att [hantera Azure-diskar](tutorial-manage-disks.md), med metodtips och prestandaförbättringar för att skapa program på datadiskar i stället för OS-disken.

### <a name="create-scale-set-with-data-disks"></a>Skapa en skalningsuppsättning med datadiskar
Om du vill skapa en skalningsuppsättning och ansluta datadiskar, lägger du till parametern `--data-disk-sizes-gb` i kommandot [az vmss create](/cli/azure/vmss#az_vmss_create). I följande exempel skapas en skalningsuppsättning med *50* Gb datadiskar anslutna till varje instans:

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

När instanser tas bort från en skalningsuppsättning, tas eventuella anslutna datadiskar också bort.

### <a name="add-data-disks"></a>Lägga till datadiskar
Lägg till en datadisk till instanser i din skalningsuppsättning med hjälp av [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach). I följande exempel läggs en *50* Gb disk till i varje instans:

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>Koppla från datadiskar
Om du vill ta bort en datadisk i instanser i din skalningsuppsättning använder du [az vmss disk detach](/cli/azure/vmss/disk#az_vmss_disk_detach). I följande exempel tar vi bort datadisken på LUN *2* från varje instans:

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>Nästa steg
I självstudien skapade du en VM-skalningsuppsättning. Du har lärt dig att:

> [!div class="checklist"]
> * Använda cloud-init för att skapa ett skalbart program
> * Skapa en VM-skalningsuppsättning
> * Öka eller minska antalet instanser i en skalningsuppsättning
> * Skapa regler för automatisk skalning
> * Visa anslutningsinformation för skalningsuppsättningsinstanser
> * Använda datadiskar i en skalningsuppsättning

Gå vidare till nästa självstudie för att lära dig mer om belastningsutjämning för virtuella datorer.

> [!div class="nextstepaction"]
> [Balansera belastningen mellan virtuella datorer](tutorial-load-balancer.md)
