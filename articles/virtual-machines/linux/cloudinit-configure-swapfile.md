---
title: "Använda molntjänster init för att konfigurera en växlingsfil på en Linux-VM | Microsoft Docs"
description: "Hur du använder molnet init för att konfigurera en växlingsfil i en Linux-VM under genereringen av med Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 88a141922f113caf7ad67c89de48f84a821f7ba3
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="use-cloud-init-to-configure-a-swapfile-on-a-linux-vm"></a>Använda molntjänster init för att konfigurera en växlingsfil på en Linux-VM
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) att konfigurera växlingsfil på olika Linux-distributioner. Växlingsfil konfigurerades traditionellt av den Linux Agent (WALA) baserat på vilka distributioner krävs en.  Det här dokumentet kommer beskriver processen för att bygga växlingsfil på begäran under etablering tid som använder molnet initiering.  Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="create-swapfile-for-ubuntu-based-images"></a>Skapa växlingsfil för Ubuntu baserat bilder
Som standard på Azure skapar inte Ubuntu galleriavbildningar växlingsfiler. Att aktivera växlingen konfiguration av fil under etablering tid med hjälp av molnet initiering av virtuell dator - finns i [AzureSwapPartitions dokument](https://wiki.ubuntu.com/AzureSwapPartitions) på Ubuntu-wikin.

## <a name="create-swapfile-for-redhat-and-centos-based-images"></a>Skapa växlingsfil för RedHat och CentOS bygger avbildningar

Skapa en fil i din aktuella shell med namnet *cloud_init_swapfile.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_swapfile.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  

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

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange molnet init-fil med `--custom-data cloud_init_swapfile.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swapfile.txt \
  --generate-ssh-keys 
```

## <a name="verify-swapfile-was-created"></a>Kontrollera växlingsfil har skapats
SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange en egen **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

När du har SSH'ed till den virtuella datorn kan du kontrollera om växlingsfil skapades

```bash
swapon -s
```

Utdata från det här kommandot ska se ut så här:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Om du har en befintlig Azure-avbildning som har en växlingsfil konfigurerats och du vill ändra konfigurationen för växlingsutrymme-filen för nya bilder, bör du ta bort befintliga växlingsfilen. Se ”anpassa bilder att etablera av molnet init-dokumentet för mer information.

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
