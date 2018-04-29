---
title: Använda molnet init för att uppdatera och installera paket i en Linux-VM på Azure | Microsoft Docs
description: Hur du använder molntjänster init för att uppdatera och installera paket i en Linux-VM under skapande av med Azure CLI 2.0
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: 8d5835b5d1b0c2f77bdf5e1a2b808478b8f4de22
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Använda molntjänster init för att uppdatera och installera paket i en Linux-VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) för att uppdatera paket på en Linux virtuella dator (VM) eller virtuella datorn anger (VMSS) vid etablering tid i Azure. Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med molnet initiering
Av säkerhetsskäl kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna på första start. Som molntjänster init fungerar även över olika Linux-distributioner, det finns ingen anledning att ange `apt` eller `yum` för package manager. I stället kan du definiera `package_upgrade` och låta molnet init processen fastställa rätt mekanism för distro används. Det här arbetsflödet kan du använda samma molnet init skripten över distributioner.

Om du vill se uppgraderingsprocessen i praktiken kan du skapa en fil i din aktuella shell med namnet *cloud_init_upgrade.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_upgrade.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange molnet init-fil med `--custom-data cloud_init_upgrade.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange en egen **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Kör verktyget för hantering av paket och Sök efter uppdateringar.

```bash
sudo yum update
```

När molnet init kontrolleras för och installerade uppdateringar på Start, bör det finnas några ytterligare uppdateringar ska tillämpas.  Du ser uppdateringsprocessen, antal ändrade paket samt installation av `httpd` genom att köra `yum history` och granska utdata som liknar den nedan.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)