---
title: Använda cloud-init för att lägga till en användare i en Virtuell Linux-dator på Azure
description: Så här använder du cloud-init för att lägga till en användare i en Virtuell Linux-dator när du skapar med Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: f1782bfe0c14e3b44703f89ec7f78590c1bb74c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969228"
---
# <a name="use-cloud-init-to-add-a-user-to-a-linux-vm-in-azure"></a>Använda cloud-init för att lägga till en användare till en Virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att lägga till en användare på en virtuell dator (VM) eller virtuella datorskaleuppsättningar (VMSS) vid etableringstid i Azure. Det här molnet-init-skriptet körs vid första start när resurserna har etablerats av Azure. Mer information om hur cloud-init fungerar inbyggt i Azure och de Linux-distributioner som stöds finns [i översikt över molnet init](using-cloud-init.md).

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Lägga till en användare i en virtuell dator med moln-init
En av de första uppgifterna på en ny Linux VM är att lägga till ytterligare en användare för dig själv för att undvika användning av *rot*. SSH-nycklar är bästa praxis för säkerhet och användbarhet. Nycklar läggs till i filen *~/.ssh/authorized_keys* med det här molnet-init-skriptet.

Om du vill lägga till en användare i en virtuell Linux-dator skapar du en fil i det aktuella skalet med namnet *cloud_init_add_user.txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_add_user.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Kontrollera att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  Du måste ange din egen offentliga nyckel (t.ex. innehållet i *~/.ssh/id_rsa.pub)* för värdet av `ssh-authorized-keys:` - det har förkortats här för att förenkla exemplet.

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
> Den #cloud-config filen innehåller `- default` parametern ingår. Detta kommer att lägga till användaren, till den befintliga administratörsanvändaren som skapats under etableringen. Om du skapar en `- default` användare utan parametern - den automatiskt genererade administratörsanvändaren som skapats av Azure-plattformen skulle skrivas över. 

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm](/cli/azure/vm) `--custom-data cloud_init_add_user.txt` skapa och ange cloud-init-filen med följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_add_user.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

Om du vill bekräfta att användaren har lagts till i den virtuella datorn och de angivna grupperna kan du visa innehållet i filen */etc/group* enligt följande:

```bash
cat /etc/group
```

Följande exempelutdata visar att användaren från *filen cloud_init_add_user.txt* har lagts till i den virtuella datorn och lämplig grupp:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
