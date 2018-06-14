---
title: Använda molnet init för att lägga till en användare till en Linux-VM på Azure | Microsoft Docs
description: Hur du använder init moln att lägga till en användare till en Linux-VM under skapande av med Azure CLI 2.0
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: ce4421fc8276f215564cb7a171a215cc166c8517
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123471"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Använda molnet init för att lägga till en användare till en Linux-VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) lägga till en användare på en virtuell dator (VM) eller virtuella datorn anger skala (VMSS) vid etablering tid i Azure. Det här molnet init skriptet körs på startas för första gången när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init översikt](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Lägga till en användare till en virtuell dator med molnet initiering
En av de första aktiviteterna på alla nya Linux-VM är att lägga till ytterligare en användare själv Undvik att använda *rot*. SSH-nycklar är bästa praxis för säkerhet och användbarhet. Nycklar som läggs till i *~/.ssh/authorized_keys* fil med det här molnet init-skriptet.

Om du vill lägga till en användare till en Linux-VM, skapar du en fil i din aktuella shell med namnet *cloud_init_add_user.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloud_init_add_user.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  Du måste ange en egen offentlig nyckel (till exempel innehållet i *~/.ssh/id_rsa.pub*) för värdet för `ssh-authorized-keys:` -den har minskats här för att förenkla exemplet.

```yaml
#cloud-config
users:
  - default
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```
> [!NOTE] 
> #Cloud-config-filen innehåller den `- default` parametern ingår. Användaren läggs till befintliga administratörsanvändare som skapades under etableringen. Om du skapar en användare utan den `- default` parameter - automatiskt genererade administratörsanvändare skapas av Azure-plattformen skulle skrivas över. 

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange molnet init-fil med `--custom-data cloud_init_add_user.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange en egen **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Bekräfta dina användare har lagts till den virtuella datorn och de angivna grupperna genom att visa innehållet i den */etc/grupp* enligt följande:

```bash
cat /etc/group
```

Följande exempel visas användaren från den *cloud_init_add_user.txt* fil har lagts till den virtuella datorn och den aktuella gruppen:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)