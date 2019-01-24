---
title: Använda cloud-init för att ange värdnamnet för en Linux-VM på Azure | Microsoft Docs
description: Hur du använder cloud-init för att anpassa en Linux VM när skapas med Azure CLI
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
ms.openlocfilehash: 140c542b71ff87f6b7a846888da06e58fa03ce10
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54855337"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Använda cloud-init för att ange värdnamnet för en Linux-VM i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att konfigurera ett visst värdnamn på en virtuell dator (VM) eller en virtuell dator av skalningsuppsättningar (VMSS) etableringstid i Azure. Skripten cloud-init körs vid den första starten när resurserna har etablerats med Azure. Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [cloud-init-översikt](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ange värdnamnet med cloud-init
Som standard är värdnamnet samma som VM-namnet när du skapar en ny virtuell dator i Azure.  Att köra ett skript för cloud-init för att ändra den här Standardvärdnamn när du skapar en virtuell dator i Azure med [az vm skapa](/cli/azure/vm), ange cloud-init-filen med den `--custom-data` växla.  

Om du vill se uppgraderingsprocessen i praktiken kan du skapa en fil i ditt nuvarande gränssnitt med namnet *cloud_init_hostname.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_hostname.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```yaml
#cloud-config
hostname: myhostname
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm skapa](/cli/azure/vm) och ange cloud-init-fil med `--custom-data cloud_init_hostname.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

När du skapat visar Azure CLI information om den virtuella datorn. Använd den `publicIpAddress` SSH till den virtuella datorn. Ange din egen adress enligt följande:

```bash
ssh <publicIpAddress>
```

Visa VM-namnet, använda den `hostname` -kommandot enligt följande:

```bash
hostname
```

Den virtuella datorn ska rapportera värdnamnet som det värde som angetts i cloud-init-fil som du ser i följande Exempelutdata:

```bash
myhostname
```

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
