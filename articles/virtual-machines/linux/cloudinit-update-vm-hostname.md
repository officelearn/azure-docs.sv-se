---
title: Använda cloud-init för att ange värdnamn för en Virtuell Linux-dator
description: Så här använder du cloud-init för att anpassa en Virtuell Linux-dator när du skapar med Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 631b8ef83d5fbf10ec401df7432b23238f2ae2e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969171"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Använda cloud-init för att ange värdnamn för en Virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att konfigurera ett specifikt värdnamn på en virtuell dator (VM) eller virtuella datorskaleuppsättningar (VMSS) vid etableringstid i Azure. Dessa cloud-init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur cloud-init fungerar inbyggt i Azure och de [Linux-distributioner](using-cloud-init.md) som stöds finns i översikt över molnet

## <a name="set-the-hostname-with-cloud-init"></a>Ange värdnamnet med molninit
Som standard är värdnamnet samma som VM-namnet när du skapar en ny virtuell dator i Azure.  Om du vill köra ett molninitskript för att ändra det här standardvärden när du skapar en `--custom-data` virtuell dator i Azure med az [vm](/cli/azure/vm)create anger du molninitfilen med växeln.  

Om du vill se uppgraderingsprocessen i praktiken skapar du en fil i det aktuella skalet med namnet *cloud_init_hostname.txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_hostname.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Kontrollera att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```yaml
#cloud-config
hostname: myhostname
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm](/cli/azure/vm) `--custom-data cloud_init_hostname.txt` skapa och ange cloud-init-filen med följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

När azure CLI har skapats visar den information om den virtuella datorn. Använd `publicIpAddress` till SSH till din virtuella dator. Ange din egen adress enligt följande:

```bash
ssh <publicIpAddress>
```

Om du vill se `hostname` vm-namnet använder du kommandot på följande sätt:

```bash
hostname
```

Den virtuella datorn ska rapportera värdnamnet som det värdet som anges i molninitfilen, vilket visas i följande exempelutdata:

```bash
myhostname
```

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
