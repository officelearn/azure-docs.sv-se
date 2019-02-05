---
title: Självstudie – Använd en anpassad virtuell datoravbildning i en skalningsuppsättning med Azure CLI | Microsoft Docs
description: Läs hur du använder Azure CLI för att skapa en anpassad virtuell datoravbildning som du kan använda för att distribuera en VM-skalningsuppsättning
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 119a53949b6184389c0e36e56732f0486c24ca5c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193496"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Självstudier: Skapa och använd en anpassad avbildning för VM-skalningsuppsättningar med Azure CLI
När du skapar en skalningsuppsättning, kan du ange en avbildning som ska användas när de virtuella datorinstanserna distribueras. Om du vill minska antalet uppgifter när de virtuella datorinstanserna distribueras, kan du använda en anpassad virtuell datoravbildning. Den här anpassade virtuella datoravbildningen inkluderar alla nödvändiga programinstallationer eller konfigurationer. Alla virtuella datorinstanser som skapats i skalningsuppsättningen använder den anpassade virtuella datoravbildningen och är redo att hantera din programtrafik. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa och anpassa en virtuell dator
> * Avetablera och generalisera den virtuella datorn
> * Skapa en anpassad virtuell datoravbildning
> * Distribuera en skalningsuppsättning som använder den anpassade virtuella datoravbildningen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste du köra Azure CLI version 2.0.29 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-and-configure-a-source-vm"></a>Skapa och konfigurera en virtuell källdator

>[!NOTE]
> Den här självstudien visar processen att skapa och använda en generaliserad VM-avbildning. Det går inte att skapa en skalningsuppsättning från en särskild VM-avbildning.

Först skapar du en resursgrupp med [az group create](/cli/azure/group#az_group_create), därefter skapar du en virtuell dator med [az vm create](/cli/azure/vm). Den här virtuella datorn används sedan som källan för en anpassad virtuell datoravbildning. Följande exempel skapar en virtuell dator som heter *myVM* i resursgruppen med namnet *myResourceGroup*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

Den offentliga IP-adressen för din virtuella dator visas i utdata från kommandot [az vm create](/cli/azure/vm). SSH till den virtuella datorns offentliga IP-adress enligt följande:

```azurecli-interactive
ssh azureuser@<publicIpAddress>
```

Nu ska vi installera en grundläggande webbserver för att anpassa din virtuella dator. När den virtuella datorinstansen i skalningsuppsättningen sen distribueras så har den alla nödvändiga paket för att köra ett webbprogram. Använd `apt-get` för att installera *NGINX* på följande sätt:

```bash
sudo apt-get install -y nginx
```

Det sista steget för att förbereda din virtuella dator för användning som en anpassad avbildning är att avetablera den virtuella datorn. Det här steget tar bort datorspecifik information från den virtuella datorn och gör det möjligt att distribuera flera virtuella datorer från en enda avbildning. När den virtuella datorn avetableras så återställs värddatornamnet till *localhost.localdomain*. SSH-värdnycklar, namnserverkonfigurationer, rotlösenord och cachelagrade DHCP-lån tas också bort.

Använd den virtuella Azure-agenten (*waagent*) för att avetablera den virtuella datorn. Den virtuella Azure-agenten installeras på varje virtuell dator och används för att kommunicera med Azure-plattformen. `-force`-parametern säger till agenten att acceptera prompter för att återställa den datorspecifika informationen.

```bash
sudo waagent -deprovision+user -force
```

Stäng din SSH-anslutning till den virtuella datorn:

```bash
exit
```


## <a name="create-a-custom-vm-image-from-the-source-vm"></a>Skapa en anpassad virtuell datoravbildning från den virtuella källdatorn
Den virtuella källdatorn är nu anpassad med Nginx-webbservern som är installerad. Nu ska vi skapa den anpassade virtuella datoravbildningen för att använda med en skalningsuppsättning.

Om du vill skapa en avbildning måste den virtuella datorn frigöras. Frigör den virtuella datorn med [az vm deallocate](/cli//azure/vm#az_vm_deallocate). Slutligen anger du tillståndet för den virtuella datorn som det generaliserats med [az vm generalize](/cli//azure/vm#az_vm_generalize) så att Azure-plattformen vet att den virtuella datorn är redo för användning i en anpassad avbildning. Du kan bara skapa en avbildning från en generaliserad virtuell dator:

```azurecli-interactive
az vm deallocate --resource-group myResourceGroup --name myVM
az vm generalize --resource-group myResourceGroup --name myVM
```

Det kan ta några minuter att frigöra och generalisera den virtuella datorn.

Nu kan du skapa en avbildning av den virtuella datorn med [az image create](/cli//azure/image). Följande exempel skapar en avbildning med namnet *myImage* från din virtuella dator:

```azurecli-interactive
az image create \
  --resource-group myResourceGroup \
  --name myImage \
  --source myVM
```


## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Skapa en skalningsuppsättning från den anpassad virtuella datoravbildningen
Skapa en skalningsuppsättning med [az vmss create](/cli/azure/vmss#az-vmss-create). Istället för en plattformsavbildning som *UbuntuLTS* eller *CentOS*, anger du namnet på din anpassade virtuella datoravbildning. Följande exempel skapar en skalningsuppsättning med namnet *myScaleSet* som använder den anpassade avbildningen med namnet *myImage* från föregående steg:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image myImage \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om vill låta trafik nå din skalningsuppsättning och verifiera att webbservern fungerar som den ska, skapar du en lastbalanseringsregel med [az network lb rule create](/cli/azure/network/lb/rule#create). Följande exempel skapar en regel med namnet *myLoadBalancerRuleWeb* som tillåter trafik på *TCP* port *80*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Om du vill se din skalningsuppsättning fungera, hämtar du den offentliga IP-adressen på din lastbalanserare med [az network public-ip show](/cli/azure/network/public-ip#show). Följande exempel hämtar IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Ange den offentliga IP-adressen i din webbläsare. Standardwebbsidan för NGINX visas som det visas i följande exempel:

![Nginx körs från en anpassad virtuell datoravbildning](media/tutorial-use-custom-image-cli/default-nginx-website.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group#az_group_delete). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien fick du läsa om hur du skapar och använder en anpassad virtuell datoravbildning för din skalningsuppsättning med Azure CLI:

> [!div class="checklist"]
> * Skapa och anpassa en virtuell dator
> * Avetablera och generalisera den virtuella datorn
> * Skapa en anpassad virtuell datoravbildning
> * Distribuera en skalningsuppsättning som använder den anpassade virtuella datoravbildningen

Gå vidare till nästa självstudie för att lära dig hur du distribuerar program till din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Distribuera program till dina skalningsuppsättningar](tutorial-install-apps-cli.md)
