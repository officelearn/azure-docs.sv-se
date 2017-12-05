---
title: "Använda molnet init för att uppdatera och installera paket i en Linux-VM på Azure | Microsoft Docs"
description: "Hur du använder molntjänster init för att uppdatera och installera paket i en Linux-VM under skapande av med Azure CLI 2.0"
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
ms.openlocfilehash: 4209bc270a6d255c8512dd6ccd5551b556da5a6b
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Använda molntjänster init för att uppdatera och installera paket i en Linux-VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) för att uppdatera paket på en Linux virtuella dator (VM) eller virtuella datorn anger (VMSS) vid etablering tid i Azure. Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med molnet initiering
Av säkerhetsskäl kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna på första start. Som molntjänster init fungerar även över olika Linux-distributioner, det finns ingen anledning att ange `apt` eller `yum` för package manager. I stället kan du definiera `package_upgrade` och låta molnet init processen fastställa rätt mekanism för distro används. Det här arbetsflödet kan du använda samma molnet init skripten över distributioner.

Om du vill se uppgraderingsprocessen i praktiken kan du skapa en fil i din aktuella shell med namnet *cloud_init_upgrade.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloud_init_upgrade.txt` att skapa filen och se en lista över tillgängliga redigerare. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och ange molnet init-fil med `--custom-data cloud_init_upgrade.txt` på följande sätt:

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

Kör verktyget för hantering av paket och Sök efter uppdateringar. I följande exempel används `apt-get` på en Ubuntu VM:

```bash
sudo apt-get upgrade
```

När molnet init kontrolleras för och installerade uppdateringar på Start, ska inga uppdateringar att gälla, som visas i följande exempel utdata:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

Du kan också visa som `httpd` har installerats genom att köra `yum history` och granska utdata refererar till `httpd`. 

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)