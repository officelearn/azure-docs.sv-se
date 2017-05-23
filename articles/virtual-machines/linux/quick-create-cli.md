---
title: "Azure snabbstart – skapa virtuell dator med CLI | Microsoft Docs"
description: "Lär dig att skapa en virtuell dator med Azure CLI."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/11/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 2bf0fa92b4f0a54738da86e2c8f26a51d053cfac
ms.contentlocale: sv-se
ms.lasthandoff: 05/11/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Skapa en virtuell Linux-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur man använder Azure CLI för att distribuera en virtuell dator som kör Ubuntu-servern. När servern har distribuerats skapas en SSH-anslutning och en NGINX-webbserver installeras.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Den här snabbstarten kräver Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator som heter *myVM*, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts enbart SSH-anslutningar till virtuella Linux-datorer distribuerade i Azure. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 från Internet. Använd kommandot [az vm open-port](/cli/azure/vm#open-port) för att öppna önskad port.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt *<publicIpAddress>* med den korrekta offentliga IP-adressen för den virtuella datorn.  I vårt exempel ovan var vår IP-adress *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installera NGINX

Använd följande bash-skript för att uppdatera paketkällor och installera det senaste NGINX-paketet. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Visa NGINX-välkomstsidan

Du kan använda en webbläsare som du väljer för att visa välkomstsidan till NGINX när NGINX är installerat och port 80 nu är öppen på en virtuell dator från Internet. Se till att använda den *publicIpAddress* som du har dokumenterat ovan för att besöka standardsidan. 

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du distribuerat en virtuell dator och en regel för nätverkssäkerhetsgrupp samt installerat en webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.


> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)

