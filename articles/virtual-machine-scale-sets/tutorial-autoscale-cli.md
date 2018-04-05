---
title: Självstudie – Skala en skalningsuppsättning automatiskt med Azure CLI 2.0 | Microsoft Docs
description: Läs hur du använder Azure CLI 2.0 för att automatiskt skala en VM-skalningsuppsättning allteftersom CPU-kraven varierar
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Självstudie: Skala en VM-skalningsuppsättning automatiskt med Azure CLI 2.0
När du skapar en skalningsuppsättning, definierar du antalet virtuella datorinstanser som du vill köra. När ditt program behöver ändras, kan du automatiskt öka eller minska antalet virtuella datorinstanser. Möjligheten att skala automatiskt låter dig hålla dig uppdaterad med kundernas behov eller svara på ändringar i programprestandan under hela livscykeln för din app. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste du köra Azure CLI version 2.0.29 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Definiera vissa parametrar för prenumerations-ID, resursgrupp och namn och plats för skalningsuppsättningen för att hjälpa att skapa reglerna för automatisk skalning:

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Skapa en resursgrupp med [az group create](/cli/azure/group#create) enligt följande:

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Skapa nu en skalningsuppsättning för en virtuell dator med [az vmss create](/cli/azure/vmss#create). Följande exempel skapar en skalningsuppsättning instansantalet *2* och genererar SSH-nycklar om de inte redan finns:

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Definiera en profil för automatisk skalning
Regler för automatisk skalning distribueras som JSON (JavaScript Object Notation) med Azure CLI 2.0. Nu ska vi titta på varje del av den här profilen för automatisk skalning och sedan skapa ett komplett exempel.

Början på profilen för automatisk skalning definierar standardvärdet, minsta och högsta kapacitet för skalningsuppsättningen. Följande exempel anger standard- och minimumkapacitet på *2* virtuella datorinstanser och högst *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>Skapa en regel för att automatiskt skala ut
Om dina programkrav ökar, ökar även belastningen på de virtuella datorinstanserna i din skalningsuppsättning. Om den här ökade belastningen är konsekvent istället för bara en kortsiktig efterfrågan, kan du konfigurera regler för automatisk skalning för att öka antalet virtuella datorinstanser i skalningsuppsättningen. När dessa virtuella datorinstanser skapas och dina program distribueras, börjar skalningsuppsättningen att distribuera trafik till dem via belastningsutjämnaren. Du kan styra vilka mått som ska övervakas, som CPU eller disk, hur länge programbelastningen måste uppfylla ett visst tröskelvärde och hur många virtuella datorinstanser som ska läggas till skalningsuppsättningen.

Nu ska vi skapa en regel som ökar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen är större än 70% över en 5 minutersperiod. När regeln utlöses, ökar antalet virtuella datorinstanser med tre.

Följande parametrar används för den här regeln:

| Parameter         | Förklaring                                                                                                         | Värde           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | Prestandamått för att övervaka och tillämpa åtgärder för skalningsuppsättningar på.                                                   | Procent CPU  |
| *timeGrain*       | Hur ofta måtten samlas in för analys.                                                                   | 1 minut        |
| *timeAggregation* | Definierar hur de insamlade mätvärdena ska aggregeras för analys.                                                | Medel         |
| *timeWindow*      | Tidsperioden som övervakas innan värdena för måttet och tröskelvärdet jämförs.                                   | 5 minuter       |
| *operator*        | Operator som används för att jämföra måttinformationen mot tröskelvärdet.                                                     | Större än    |
| *tröskelvärde*       | Det värde som får regeln för automatisk skalning att utlösa en åtgärd.                                                      | 70%             |
| *riktning*       | Anger om skalningsuppsättningen ska skala in eller ut när regeln gäller.                                              | Höj        |
| *typ*            | Anger att antalet virtuella datorinstanser ska ändras med ett specifikt värde.                                    | Ändra antal    |
| *värde*           | Hur många virtuella datorinstanser ska skalas in eller ut när regeln gäller.                                             | 3               |
| *cooldown*        | Hur lång tid ska gå innan regeln tillämpas igen så att de automatiska skalningsåtgärderna har tid att börja gälla. | 5 minuter       |

Följande exempel definierar regeln för att skala ut antalet virtuella datorinstanser. *metricResourceUri* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppnamn och namn på skalningsuppsättning:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
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
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>Skapa en regel för att automatiskt skala in
På kvällar eller helger, kan efterfrågan på ditt program minska. Om den här minskade belastningen är konsekvent över en tidsperiod, kan du konfigurera regler för automatisk skalning för att minska antalet virtuella datorinstanser i skalningsuppsättningen. Den här åtgärden för skala in minskar kostnaden för att köra din skalningsuppsättningen eftersom du bara köra de antal instanser som krävs för att uppfylla den aktuella efterfrågan.

Skapa en annan regel som minskar antalet virtuella datorinstanser i en skalningsuppsättning när den genomsnittliga CPU-belastningen faller under 30 % över en 5 minutersperiod. Följande exempel definierar regeln för att skala in antalet virtuella datorinstanser med en. *metricResourceUri* använder de variabler som tidigare definierats för prenumerations-ID, resursgruppnamn och namn på skalningsuppsättning:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
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
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Tillämpa automatiska skalningsregler för en skalningsuppsättning
Det sista steget är att använda profilen och reglerna för automatisk skalning på din skalningsuppsättning. Din skalningsuppsättning kan därefter automatiskt skala in eller ut baserat på programmets efterfrågan. Använd profilen för automatisk skalning med [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) på följande sätt. Följande fullständiga JSON använder den profil och de regler som angavs i föregående avsnitt:

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
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
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
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
                "value": "3",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
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
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Generera CPU-belastning på skalningsuppsättningen
Om du vill testa reglerna för automatisk skalning kan du generera lite CPU-belastning på de virtuella datorinstanserna i skalningsuppsättningen. Den här simulerade CPU-belastningen gör att de automatiska skalningarna skalar ut och ökar antalet virtuella datorinstanser. När den simulerade CPU-belastningen sedan minskar så skalar reglerna för automatisk skalning in och minskar antalet virtuella datorinstanser.

Först, listar du adressen och portarna för att ansluta till virtuella datorinstanser i en skalningsuppsättning med [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

Följande exempelutdata visar instansnamnet, den offentliga IP-adressen för belastningsutjämnaren och portnummer som NAT (Network Address Translation)-regler vidarebefordrar trafik till:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH till din första virtuella datorinstans. Ange din egen offentliga IP-adress och portnummer med parametern `-p`, som det visas i föregående kommando:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

När du är inloggad, installerar du **stress**-verktyget. Starta *10* **stress**-arbetare som genererar CPU-belastning. De här arbetarna kör i *420* sekunder, vilket räcker för att få reglerna för automatisk skalning att implementera den önskade åtgärden.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

När **stress** visar utdata som liknar *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, trycker du på *Retur* för att återgå till prompten.

Kontrollera att **stress** genererar CPU-belastning genom att granska den aktiva systembelastningen med **top**-verktyget:

```azuecli-interactive
top
```

Avsluta **top**, stäng sedan anslutningen till den virtuella datorinstansen. **stress** fortsätter att köras på den virtuella datorinstansen.

```azurecli-interactive
Ctrl-c
exit
```

Anslut till en andra virtuell datorinstans med det portnummer som listas från den föregående [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Installera och kör **stress** och starta sedan tio arbetare på den här andra virtuella datorinstansen.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

När **stress** återigen visar utdata som liknar *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, trycker du på *Retur* för att återgå till prompten.

Stäng din anslutning till den andra virtuella datorinstansen. **stress** fortsätter att köras på den virtuella datorinstansen.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Övervaka de aktiva reglerna för automatisk skalning
Du övervakar antalet virtuella datorinstanser i din skalningsuppsättning med **watch**. Det tar 5 minuter för reglerna för automatisk skalning att börja utskalningsprocessen till svar på den CPU-belastning som skapas av **stress** på var och en av de virtuella datorinstanserna:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

När CPU-tröskelvärdet har uppnåtts, ökar reglerna för automatisk skalning antalet virtuella datorinstanser i skalningsuppsättningen. Följande utdata visar tre virtuella datorer som skapats när skalningsuppsättningen skalar ut automatiskt:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

När **stress** stoppar på den första virtuella datorn, återgår den genomsnittliga CPU-belastningen till normal. Efter 5 minuter till, skalar reglerna för automatisk skalning in antalet virtuella datorinstanser. Skalan in-åtgärder tar först bort de virtuella datorinstanserna med de högsta ID. Följande exempelutdata visar en virtuell datorinstans som tas bort eftersom skalningsuppsättningen skalar in automatiskt:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Avsluta *watch* med `Ctrl-c`. Skalningsuppsättningen fortsätter att skala in var 5:e minut och tar bort en virtuell datorinstans tills att det lägsta instansantalet på två har uppnåtts.


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group#az_group_delete). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien läste du om hur du automatiskt kan skala in eller ut en skalningsuppsättning med Azure CLI 2.0:

> [!div class="checklist"]
> * Använd automatisk skalning med en skalningsuppsättning
> * Skapa och använd regler för automatisk skalning
> * Belastningstesta virtuella datorinstanser och utlös regler för automatisk skalning
> * Skala tillbaka automatiskt när efterfrågan minskar

Fler exempel på VM-skalningsuppsättningar i praktiken finns i följande exempelskript för Azure CLI 2.0:

> [!div class="nextstepaction"]
> [Skriptexempel på skalningsuppsättningar för Azure CLI 2.0](cli-samples.md)