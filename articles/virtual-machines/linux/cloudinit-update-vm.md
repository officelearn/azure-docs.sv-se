---
title: Använda cloud-init i en Virtuell Linux-dator på Azure
description: Så här använder du cloud-init för att uppdatera och installera paket i en Virtuell Linux-dator när du skapar med Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969143"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Använda cloud-init för att uppdatera och installera paket i en Virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att uppdatera paket på en Virtuell Linux-dator (VM) eller virtuella datorskalauppsättningar vid etableringstid i Azure. Dessa cloud-init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur cloud-init fungerar inbyggt i Azure och de [Linux-distributioner](using-cloud-init.md) som stöds finns i översikt över molnet

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med moln-init
Av säkerhetsskäl kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna vid första uppstarten. Eftersom cloud-init fungerar över olika Linux-distributioner behöver `apt` `yum` du inte ange eller för pakethanteraren. I stället `package_upgrade` definierar och låter du moln-init-processen bestämma lämplig mekanism för de distributioner som används. Med det här arbetsflödet kan du använda samma moln-init-skript över distributioner.

Om du vill se uppgraderingsprocessen i praktiken skapar du en fil i det aktuella skalet med namnet *cloud_init_upgrade.txt* och klistrar in följande konfiguration. I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor cloud_init_upgrade.txt` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Kontrollera att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm](/cli/azure/vm) `--custom-data cloud_init_upgrade.txt` skapa och ange cloud-init-filen med följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

Kör pakethanteringsverktyget och sök efter uppdateringar.

```bash
sudo yum update
```

Eftersom cloud-init kontrolleras för och installerade uppdateringar vid uppstart, bör det inte finnas några ytterligare uppdateringar att tillämpa.  Du ser uppdateringsprocessen, antalet ändrade paket samt installation `httpd` av `yum history` genom att köra och granska utdata som liknar den nedan.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
