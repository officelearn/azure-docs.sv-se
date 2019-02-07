---
title: Använda cloud-init för att uppdatera och installera paket i en Linux-VM på Azure | Microsoft Docs
description: Hur du använder cloud-init för att uppdatera och installera paket i en Linux VM när skapas med Azure CLI
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
ms.openlocfilehash: d5f4dc7f4abc13f253a206a63e65faf1106f9c7c
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766188"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Använda cloud-init för att uppdatera och installera paket i en Linux-VM i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att uppdatera paket på en Linux-dator (VM) eller VM-skalningsuppsättning anger (VMSS) vid etableringstid i Azure. Skripten cloud-init körs vid den första starten när resurserna har etablerats med Azure. Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [cloud-init-översikt](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med cloud-init
Av säkerhetsskäl kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna vid första start. Eftersom cloud-init fungerar över olika Linux-distributioner, det finns ingen anledning att ange `apt` eller `yum` för package manager. I stället definierar du `package_upgrade` och låta cloud-init-processen bestämma lämpliga mekanism för distribution används. Det här arbetsflödet kan du använda samma cloud-init-skripten för distributioner.

Om du vill se uppgraderingsprocessen i praktiken kan du skapa en fil i ditt nuvarande gränssnitt med namnet *cloud_init_upgrade.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_upgrade.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm skapa](/cli/azure/vm) och ange cloud-init-fil med `--custom-data cloud_init_upgrade.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn visas i utdata från föregående kommando. Ange ett eget **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Kör paketet hanteringsverktyg och Sök efter uppdateringar.

```bash
sudo yum update
```

Eftersom cloud-init markerat för och installerade uppdateringar vid starten, bör det finnas inga ytterligare uppdateringar tillämpas.  Du ser uppdateringsprocessen, antal ändrade paket samt installation av `httpd` genom att köra `yum history` och granska utdata som liknar den som visas nedan.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
