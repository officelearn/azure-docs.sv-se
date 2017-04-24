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
ms.date: 04/03/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: d051382954db989ce4152602d249383e7b9dfa46
ms.lasthandoff: 04/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli"></a>Skapa en virtuell Linux-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här guiden beskriver hur man använder Azure CLI för att distribuera en virtuell dator som kör Ubuntu 16.04 LTS. När servern har distribuerats för vi SSH till den virtuella datorn för att kunna installera NGINX. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/en-us/free/?WT.mc_id=A261C142F) innan du börjar.

Kontrollera även att Azure CLI har installerats. Mer information finns i [installationsguiden för Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure-prenumerationen med kommandot [az login](/cli/azure/#login) och följ anvisningarna på skärmen.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#create). 

Följande exempel skapar en virtuell dator som heter `myVM`, och SSH-nycklar skapas om de inte redan finns på en standardnyckelplats. Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

När den virtuella datorn har skapats visar Azure CLI information som ser ut ungefär som i följande exempel. Anteckna `publicIpAddress`. Den här adressen används för att få åtkomst till den virtuella datorn.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik 

Som standard tillåts enbart SSH-anslutningar till virtuella Linux-datorer distribuerade i Azure. Om den här virtuella datorn kommer att vara en webbserver måste du öppna port 80 från Internet.  Ett enda kommando krävs för att öppna den önskade porten.  
 
 ```azurecli 
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Använd följande kommando för att skapa en SSH-session med den virtuella datorn. Ersätt `<publicIpAddress>` med den korrekta offentliga IP-adressen för den virtuella datorn.  I vårt exempel ovan var vår IP-adress `40.68.254.142`.

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

## <a name="view-the-ngix-welcome-page"></a>Visa NGIX-välkomstsidan

Du kan använda en webbläsare som du väljer för att visa välkomstsidan till NGINX när NGINX är installerat och port 80 nu är öppen på en virtuell dator från Internet. Se till att använda den `publicIpAddress` som du har dokumenterat ovan för att besöka standardsidan. 

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png) 


## <a name="delete-virtual-machine"></a>Ta bort en virtuell dator

När den inte längre behövs kan följande kommando användas för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudie: Skapa virtuella datorer med hög tillgänglighet](create-cli-complete.md)

[Utforska exempel på distribution av virtuella datorer med CLI](cli-samples.md)

