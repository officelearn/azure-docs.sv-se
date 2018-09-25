---
title: Använda cloud-init för att lägga till en användare i en Linux-VM på Azure | Microsoft Docs
description: Hur du använder cloud-init för att lägga till en användare till en Linux VM när skapas med Azure CLI
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
ms.openlocfilehash: 905d701437b1b580c019c800d13b18f725580fdd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972954"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Använda cloud-init för att lägga till en användare i en Linux-VM i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) lägga till en användare på en virtuell dator (VM) eller en virtuell dator av skalningsuppsättningar (VMSS) etableringstid i Azure. Det här cloud-init-skriptet körs vid den första starten när resurserna har etablerats med Azure. Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [översikt över cloud-init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Lägga till en användare till en virtuell dator med cloud-init
En av de första uppgifterna på alla nya Linux-VM är att lägga till ytterligare en användare själv Undvik att använda *rot*. SSH-nycklar är bästa praxis för säkerhet och användbarhet. Nycklar har lagts till i *~/.ssh/authorized_keys* fil med det här cloud-init-skriptet.

Om du vill lägga till en användare i en Linux VM, kan du skapa en fil i ditt nuvarande gränssnitt med namnet *cloud_init_add_user.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_add_user.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  Du måste ange en egen offentlig nyckel (till exempel innehållet i *~/.ssh/id_rsa.pub*) för värdet för `ssh-authorized-keys:` -den har kortats ned här för att förenkla exemplet.

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
> #Cloud-config-filen innehåller den `- default` parameter som ingår. Detta läggs användaren till befintliga administratörsanvändare som skapades under etableringen. Om du skapar en användare utan den `- default` parametern - automatiskt genererade administratörsanvändaren skapats av Azure-plattformen skulle skrivas över. 

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange cloud-init-fil med `--custom-data cloud_init_add_user.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn visas i utdata från föregående kommando. Ange ett eget **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Bekräfta dina användare har lagts till den virtuella datorn och de angivna grupperna genom att visa innehållet i den */etc/grupp* så här:

```bash
cat /etc/group
```

Följande Exempelutdata visar användaren från den *cloud_init_add_user.txt* fil har lagts till den virtuella datorn och lämplig grupp:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)