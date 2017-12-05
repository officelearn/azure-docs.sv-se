---
title: "Använd molnet init för att ange värdnamnet för en Linux-VM på Azure | Microsoft Docs"
description: "Hur du använder molntjänster init för att anpassa en Linux VM under genereringen av med Azure CLI 2.0"
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
ms.openlocfilehash: 7455748b2e08104dadfed8cfe41581a400539d4f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Använd molnet init för att ange värdnamnet för en Linux-VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) för att konfigurera specifika värdnamn på en virtuell dator (VM) eller virtuella datorn anger skala (VMSS) vid etablering tid i Azure. Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Ange värdnamnet med molnet initiering
Som standard är värdnamnet samma som namnet på virtuella datorn när du skapar en ny virtuell dator i Azure.  Att köra ett moln init-skript om du vill ändra standard värdnamnet när du skapar en virtuell dator i Azure med [az vm skapa](/cli/azure/vm#create), ange molnet init-filen med den `--custom-data` växla.  

Om du vill se uppgraderingsprocessen i praktiken kan du skapa en fil i din aktuella shell med namnet *cloud_init_hostname.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor cloud_init_hostname.txt` att skapa filen och se en lista över tillgängliga redigerare. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  

```yaml
#cloud-config
hostname: myhostname
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och ange molnet init-fil med `--custom-data cloud_init_hostname.txt` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

När du skapat visas information om den virtuella datorn i Azure CLI. Använd den `publicIpAddress` till SSH till den virtuella datorn. Ange din egen adress enligt följande:

```bash
ssh <publicIpAddress>
```

Namnet på virtuella datorn, Använd den `hostname` kommandot på följande sätt:

```bash
hostname
```

Den virtuella datorn bör rapportera värdnamnet som det värde som anges i molnet init-filen som visas i följande exempel utdata:

```bash
myhostname
```

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)