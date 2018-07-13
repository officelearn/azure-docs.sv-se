---
title: Snabbstart–-Skapa en virtuell Linux-dator med Azure CLI 2.0 | Microsoft Docs
description: I den här snabbstarten lär du dig hur du använder Azure CLI 2.0 för att skapa en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6536860bb75d068a96899f2d30ec7a6126a28436
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37927639"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Snabbstart: Skapa en virtuell Linux-dator med Azure CLI 2.0

Azure CLI 2.0 används till att skapa och hantera Azure-resurser från kommandoraden eller i skript. Den här snabbstarten beskriver hur du använder Azure CLI 2.0 för att distribuera en virtuell dator (VM) för Linux i Azure som kör Ubuntu. För att se hur den virtuella datorn fungerar i praktiken använder du sedan SSH för att ansluta till den virtuella datorn och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare under den här snabbstarten. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az_group_create). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm#az_vm_create).

Följande exempel skapar en virtuell dator med namnet *myVM*, lägger till ett användarkonto med namnet *azureuser* och genererar SSH-nycklar om de inte redan finns på standardplatsen för nycklar (*~/.ssh*). Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Skriv ned din egen `publicIpAddress` i utdataresultatet från din virtuella dator. Den här adressen används för att ansluta till den virtuella datorn i nästa steg.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard öppnas SSH-anslutningar när du skapar en virtuell Linux-dator i Azure. Använd [az vm open-port](/cli/azure/vm#az_vm_open_port) för att öppna TCP port 80 för användning med NGINX-webbservern:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

SSH till den virtuella datorn som normalt. Ersätt **publicIpAddress** med offentliga IP-adressen för den virtuella datorn enligt beskrivningen i föregående utdata från den virtuella datorn:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Om du vill uppdatera paketkällorna och installera det senaste NGINX-paketet kör du följande kommandon från SSH-sessionen:

```bash
# update packages
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen med `exit`.

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

När NGINX har installerats och port 80 är öppen på den virtuella datorn från Internet använder du valfri webbläsare för att visa standardvälkomstsidan för NGINX. Använd den virtuella datorns offentliga IP-adress, som du hämtade i ett tidigare steg. Följande exempel visar NGINX-standardwebbplatsen:

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group#az_group_delete) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser. Se till att du har avslutat SSH-session till den virtuella datorn och ta sedan bort resurserna på följande sätt:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.


> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
