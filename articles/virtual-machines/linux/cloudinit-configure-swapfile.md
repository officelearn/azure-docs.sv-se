---
title: Använda cloud-init för att konfigurera en växlingspartition på en Virtuell Linux-dator
description: Så här använder du cloud-init för att konfigurera en växlingspartition i en Virtuell Linux-dator när du skapar med Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969207"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Använda cloud-init för att konfigurera en växlingspartition på en Virtuell Linux-dator
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att konfigurera växlingspartitionen på olika Linux-distributioner. Växlingspartitionen konfigurerades traditionellt av Linux Agent (WALA) baserat på vilken distributioner krävde en.  Det här dokumentet beskriver processen för att skapa växlingspartitionen på begäran under etableringstiden med hjälp av cloud-init.  Mer information om hur cloud-init fungerar inbyggt i Azure och de [Linux-distributioner](using-cloud-init.md) som stöds finns i översikt över molnet

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Skapa växlingspartition för Ubuntu-baserade bilder
Som standard på Azure skapar Ubuntu-galleriavbildningar inte växlingspartitioner. Så här aktiverar du konfiguration av växlingspartitioner under vm-etablering med cloud-init – se [AzureSwapPartitions-dokumentet](https://wiki.ubuntu.com/AzureSwapPartitions) på Ubuntu-wikin.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Skapa växlingspartition för Red Hat- och CentOS-baserade bilder

Skapa en fil i det aktuella skalet med namnet *cloud_init_swappart.txt* och klistra in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_swappart.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Kontrollera att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

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

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm](/cli/azure/vm) `--custom-data cloud_init_swappart.txt` skapa och ange cloud-init-filen med följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Verifiera att växlingspartitionen skapades
SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

När du har SSH'ed i den virtuella datorn, kontrollera om växlingspartitionen skapades

```bash
swapon -s
```

Utdata från det här kommandot ska se ut så här:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Om du har en befintlig Azure-avbildning som har en växlingspartition konfigurerad och du vill ändra konfigurationen för växlingspartition för nya avbildningar, bör du ta bort den befintliga växlingspartitionen. Se "Anpassa bilder för att etablera efter cloud-init"-dokument för mer information.

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
