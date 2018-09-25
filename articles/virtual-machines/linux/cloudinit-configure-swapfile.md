---
title: Använda cloud-init för att konfigurera en växlingsfil på en Linux VM | Microsoft Docs
description: Hur du använder cloud-init för att konfigurera en växlingsfil i en Linux VM när skapas med Azure CLI
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
ms.openlocfilehash: 2a5a878b7c8c3b6126d90b978241fbcb237d8db7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946314"
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Använda cloud-init för att konfigurera en växlingsfil på en Linux VM
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) att konfigurera växlingsfil på olika Linux-distributioner. Växlingsfil konfigurerades traditionellt genom den Linux Agent (WALA) baserat på vilka distributioner krävs en.  Det här dokumentet beskriver processen för att skapa växlingsfil på begäran under etableringstid med cloud-init.  Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [cloud-init-översikt](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Skapa växlingsfil för Ubuntu-baserade avbildningar
Som standard på Azure skapa inte Ubuntu galleriavbildningar swap-filer. Att aktivera swap-filkonfiguration under VM-etableringstillstånd tid på att använda cloud-init - finns på den [AzureSwapPartitions dokumentet](https://wiki.ubuntu.com/AzureSwapPartitions) på Ubuntu wiki.

## <a name="create-swapfile-for-red-hat-and-centos-based-images"></a>Skapa växlingsfil för Red Hat och CentOS-baserade avbildningar

Skapa en fil i ditt nuvarande gränssnitt med namnet *cloud_init_swapfile.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_swapfile.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange cloud-init-fil med `--custom-data cloud_init_swapfile.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Kontrollera växlingsfil har skapats
SSH till den offentliga IP-adressen för den virtuella datorn visas i utdata från föregående kommando. Ange ett eget **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

När du har SSH'ed på den virtuella datorn kan du kontrollera om växlingsfil har skapats

```bash
swapon -s
```

Utdata från det här kommandot ska se ut så här:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Om du har en befintlig Azure-avbildning som har en växlingsfil som konfigurerats och du vill ändra konfigurationen för swap-fil för nya avbildningar, bör du ta bort befintliga växlingsfilen. Se ”anpassa avbildningar kan etablera av cloud-init-dokumentet för mer information.

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
