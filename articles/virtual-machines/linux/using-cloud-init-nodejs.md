---
title: Med hjälp av molnet init för att anpassa en Linux VM under Skapa i Azure | Microsoft Docs
description: Hur du använder molntjänster init för att anpassa en Linux VM under genereringen av med Azure CLI 1.0
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: v-livech
ms.openlocfilehash: 2e9182a18a2827ed7f54f5fd042e5934b3b1fd5c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918446"
---
# <a name="use-cloud-init-to-customize-a-linux-vm-during-creation-with-the-azure-cli-10"></a>Använda molntjänster init för att anpassa en Linux VM under skapande av med Azure CLI 1.0
Den här artikeln visar hur du gör ett moln init-skript för att ange värdnamnet, uppdateringspaket installeras och hanterar användarkonton.  Molnet init-skript anropas under den virtuella dator skapandet Azure CLI.  Artikeln kräver:

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)).
* [Azure CLI](../../cli-install-nodejs.md) inloggad med `azure login`.
* Azure CLI *måste vara i* Azure Resource Manager-läge `azure config mode arm`.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands) – våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering

## <a name="quick-commands"></a>Snabbkommandon
Skapa ett moln init.txt skript som anger värdnamnet, uppdaterar alla paket och lägger till en användare med sudo Linux.

```sh
#cloud-config
hostname: myVMhostname
apt_upgrade: true
users:
  - name: myNewAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myVM
```
Skapa en resursgrupp om du vill starta virtuella datorer i.

```azurecli
azure group create myResourceGroup westus
```

Skapa en Linux VM som använder molnet init konfigureras under start.

```azurecli
azure vm create \
  -g myResourceGroup \
  -n myVM \
  -l westus \
  -y Linux \
  -f myVMnic \
  -F myVNet \
  -P 10.0.0.0/22 \
  -j mySubnet \
  -k 10.0.0.0/24 \
  -Q canonical:ubuntuserver:14.04.2-LTS:latest \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -C cloud-init.txt
```

## <a name="detailed-walkthrough"></a>Detaljerad genomgång
### <a name="introduction"></a>Introduktion
När du startar en ny Linux VM får du en standard Linux VM utan något anpassad eller redo för dina behov. [Molnet init](https://cloudinit.readthedocs.org) är ett standardiserat sätt att mata in ett skript eller konfiguration inställningar i den Linux VM som startar för första gången.

I Azure, det finns tre olika sätt att göra ändringar på en Linux VM när den distribuerats eller startas.

* Mata in skript med hjälp av molnet initiering.
* Mata in skript med hjälp av Azure [VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* En Azure-mall med hjälp av molnet initiering.
* En Azure-mall med hjälp av [CustomScriptExtention](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Att mata in skript när som helst efter omstart:

* SSH kan köra kommandon direkt
* Mata in skript med hjälp av Azure [VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), imperatively eller i en Azure-mall
* Verktyg för konfigurationshantering som Ansible, Salt, Chef eller Puppet.

> [!NOTE]
> : VMAccess-tillägget körs ett skript som rot på samma sätt som med SSH kan.  Med hjälp av VM-tillägget kan dock flera funktioner som Azure-erbjudanden som kan vara användbara beroende på ditt scenario.
> 
> 

## <a name="cloud-init-availability-on-azure-vm-quick-create-image-aliases"></a>Molnet init tillgänglighet på Virtuella Azure-Snabbregistrering avbildningen alias:
| Alias | Utgivare | Erbjudande | SKU | Version | cloud-init |
|:--- |:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |senaste |nej |
| CoreOS |CoreOS |CoreOS |Stable |senaste |ja |
| Debian |credativ |Debian |8 |senaste |nej |
| openSUSE |SUSE |openSUSE |13.2 |senaste |nej |
| RHEL |Redhat |RHEL |7.2 |senaste |nej |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |senaste |ja |

Microsoft arbetar med våra partner att hämta molnet initiering ingår och arbeta med bilder som skickas till Azure.

## <a name="adding-a-cloud-init-script-to-the-vm-creation-with-the-azure-cli"></a>Lägga till ett moln init-skript i Skapa Virtuella med Azure CLI
Om du vill starta ett moln init-skript när du skapar en virtuell dator i Azure, ange molnet init-filen med hjälp av Azure CLI `--custom-data` växla.

Skapa en resursgrupp om du vill starta virtuella datorer i.

```azurecli
azure group create myResourceGroup westus
```

Skapa en Linux VM som använder molnet init konfigureras under start.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubnet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud-init.txt
```

## <a name="creating-a-cloud-init-script-to-set-the-hostname-of-a-linux-vm"></a>Skapa ett moln init-skript för att ange värdnamnet för en Linux-VM
En av de enklaste och mest viktiga inställningarna för alla Linux-VM är värdnamnet. Vi kan enkelt ange molnet initiering med det här skriptet.  

### <a name="example-cloud-init-script-named-cloudconfighostnametxt"></a>Exempel molnet init skript som heter `cloud_config_hostname.txt`.
```sh
#cloud-config
hostname: myservername
```

Under den första starten av den virtuella datorn anger skriptet molnet init värdnamnet till `myservername`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_hostname.txt
```

Logga in och kontrollera värdnamnet för den nya virtuella datorn.

```bash
ssh myVM
hostname
myservername
```

## <a name="creating-a-cloud-init-script-to-update-linux"></a>Skapa en moln-init-skript för att uppdatera Linux
Av säkerhetsskäl vill du din Ubuntu VM att uppdatera startas första gången.  Med hjälp av molnet init vi kan göra det med följande skript, beroende på Linux-distribution som du använder.

### <a name="example-cloud-init-script-cloudconfigaptupgradetxt-for-the-debian-family"></a>Molnet init exempelskriptet `cloud_config_apt_upgrade.txt` Debian-familjen
```sh
#cloud-config
apt_upgrade: true
```

Efter Linux har startats, installerade paket uppdateras `apt-get`.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_apt_upgrade.txt
```

Logga in och kontrollera att alla paket har uppdaterats.

```bash
ssh myUbuntuVM
sudo apt-get upgrade
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
The following packages have been kept back:
  linux-generic linux-headers-generic linux-image-generic
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="creating-a-cloud-init-script-to-add-a-user-to-linux"></a>Skapa ett moln init-skript för att lägga till en användare till Linux
En av de första uppgifterna på alla nya Linux-VM är att lägga till en användare själv eller Undvik att använda `root`. SSH-nycklar är bästa praxis för säkerhet och användbarhet och de läggs till i `~/.ssh/authorized_keys` fil med det här molnet init-skriptet.

### <a name="example-cloud-init-script-cloudconfigadduserstxt-for-debian-family"></a>Molnet init exempelskriptet `cloud_config_add_users.txt` Debian-familjen
```sh
#cloud-config
users:
  - name: myCloudInitAddedAdminUser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>==myAdminUser@myUbuntuVM
```

Efter Linux har startats, skapas och läggs till i sudo-gruppen Alla användare.

```azurecli
azure vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --location westus \
  --os-type Linux \
  --nic-name myVMnic \
  --vnet-name myVNet \
  --vnet-address-prefix 10.0.0.0/22 \
  --vnet-subnet-name mySubNet \
  --vnet-subnet-address-prefix 10.0.0.0/24 \
  --image-urn canonical:ubuntuserver:14.04.2-LTS:latest \
  --ssh-publickey-file ~/.ssh/id_rsa.pub \
  --admin-username myAdminUser \
  --custom-data cloud_config_add_users.txt
```

Logga in och kontrollera den nyligen skapade användaren.

```bash
ssh myVM
cat /etc/group
```

Resultat

```bash
root:x:0:
<snip />
sudo:x:27:myCloudInitAddedAdminUser
<snip />
myCloudInitAddedAdminUser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Initiering molnet har blivit ett sätt som standard för att ändra din Linux VM på Start. Azure har också VM-tillägg som gör att du kan ändra din LinuxVM på Start eller medan den körs. Du kan till exempel använda Azure-VMAccessExtension för att återställa SSH-eller medan den virtuella datorn körs. Med molnet initiering måste startas om för att återställa lösenordet.

[Om virtuella datortillägg och funktioner](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Azure Linux-datorer med hjälp av VMAccess-tillägget](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

