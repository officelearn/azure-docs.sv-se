---
title: "Använda molntjänster init för att anpassa en Linux VM | Microsoft Docs"
description: "Hur du använder molntjänster init för att anpassa en Linux VM under genereringen av med Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/03/2017
ms.author: iainfou
ms.openlocfilehash: 5559f258f5c29b07edb5e61be4755d67173019e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-cloud-init-to-customize-a-linux-vm-in-azure"></a>Använda molntjänster init för att anpassa en Linux VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) uppdateringspaket om du vill ange värdnamnet och hantera användarkonton på en virtuell dator (VM) i Azure. Skripten molnet init körs på Start när du skapar en virtuell dator med Azure CLI 2.0. En mer detaljerad översikt över hur du installerar program, skriva konfigurationsfiler och mata in nycklar från Nyckelvalvet finns [självstudierna](tutorial-automate-vm-deployment.md). Du kan också utföra dessa steg med [Azure CLI 1.0](using-cloud-init-nodejs.md).


## <a name="cloud-init-overview"></a>Översikt över molntjänster initiering
[Molnet init](https://cloudinit.readthedocs.io) är ett vanligt sätt att anpassa en Linux VM när den startas för första gången. Du kan använda molnet init för att installera paket och skriva filer eller för att konfigurera användare och säkerhet. Eftersom molnet init körs under den ursprungliga startprocessen, det inte finns några ytterligare steg krävs agenter att tillämpa konfigurationen.

Molnet init fungerar även över distributioner. Exempelvis kan du inte använda **lgh get installera** eller **yum installera** att installera ett paket. I stället kan du definiera en lista över paket som ska installeras. Molnet init används automatiskt det ursprungliga paket hanteringsverktyget för distro som du väljer.

Vi arbetar med våra partner att hämta molnet initiering ingår och arbeta med bilder som skickas till Azure. I följande tabell beskrivs aktuella molnet init tillgängligheten på Azure-plattformen bilder:

| Alias | Utgivare | Erbjudande | SKU | Version |
|:--- |:--- |:--- |:--- |:--- |:--- |
| UbuntuLTS |Canonical |UbuntuServer |16.04 LTS |senaste |
| UbuntuLTS |Canonical |UbuntuServer |14.04.5-LTS |senaste |
| CoreOS |CoreOS |CoreOS |Stable |senaste |


## <a name="set-the-hostname-with-cloud-init"></a>Ange värdnamnet med molnet initiering
Molnet init-filer skrivs i [YAML](http://www.yaml.org). Att köra ett moln init-skript när du skapar en virtuell dator i Azure med [az vm skapa](/cli/azure/vm#create), ange molnet init-filen med den `--custom-data` växla. Nu ska vi titta på några exempel på vad du kan konfigurera med en moln-init-fil. Ett vanligt scenario är att ange värdnamnet för en virtuell dator. Som standard är värdnamnet samma som namnet på virtuella datorn. 

Börja med att skapa en resursgrupp med [az gruppen skapa](/cli/azure/group#create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i den *eastus* plats:

```azurecli
az group create --name myResourceGroup --location eastus
```

Skapa en fil med namnet i din aktuella shell *cloud_init_hostname.txt* och klistra in följande konfiguration. Till exempel skapa filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange i molnet-gränssnittet `sensible-editor cloud_init_hostname.txt` att skapa filen och se en lista över tillgängliga redigerare. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden:

```yaml
#cloud-config
hostname: myhostname
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och ange molnet init-fil med `--custom-data cloud_init_hostname.txt` på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMHostname \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_hostname.txt
```

När du skapat visas information om den virtuella datorn i Azure CLI. Använd den `publicIpAddress` till SSH till den virtuella datorn. Ange din egen adress enligt följande:

```bash
ssh azureuser@publicIpAddress
```

Namnet på virtuella datorn, Använd den `hostname` kommandot på följande sätt:

```bash
hostname
```

Den virtuella datorn bör rapportera värdnamnet som det värde som anges i molnet init-filen som visas i följande exempel utdata:

```bash
myhostname
```

## <a name="update-a-vm-with-cloud-init"></a>Uppdatera en virtuell dator med molnet initiering
Av säkerhetsskäl kanske du vill konfigurera en virtuell dator för att tillämpa de senaste uppdateringarna på första start. Som molntjänster init fungerar även över olika Linux-distributioner, det finns ingen anledning att ange `apt` eller `yum` för package manager. I stället kan du definiera `package_upgrade` och låta molnet init processen fastställa rätt mekanism för distro används. Det här arbetsflödet kan du använda samma molnet init skripten över distributioner.

Om du vill se uppgraderingsprocessen i praktiken, skapa en moln-init-fil med namnet *cloud_init_upgrade.txt* och klistra in följande konfiguration:

```yaml
#cloud-config
package_upgrade: true
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och ange molnet init-fil med `--custom-data cloud_init_upgrade.txt` på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUpgrade \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_upgrade.txt
```

SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange dina egna offentliga IP-adressen på följande sätt:

```bash
ssh azureuser@publicIpAddress
```

Kör verktyget för hantering av paket och Sök efter uppdateringar. I följande exempel används `apt-get` på en Ubuntu VM:

```bash
sudo apt-get upgrade
```

När molnet init kontrolleras för och installerade uppdateringar på Start, finns det inga uppdateringar att gälla, som visas i följande exempel utdata:

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

## <a name="add-a-user-to-a-vm-with-cloud-init"></a>Lägga till en användare till en virtuell dator med molnet initiering
En av de första aktiviteterna på alla nya Linux-VM är att lägga till en användare själv Undvik att använda *rot*. SSH-nycklar är bästa praxis för säkerhet och användbarhet. Nycklar som läggs till i *~/.ssh/authorized_keys* fil med det här molnet init-skriptet.

Om du vill lägga till en användare till en Linux-VM, skapa en moln-init-fil med namnet *cloud_init_add_user.txt* och klistra in följande konfiguration. Ange dina egna offentliga nyckeln (till exempel innehållet i *~/.ssh/id_rsa.pub*) för *ssh-behörighet-nycklar*:

```yaml
#cloud-config
users:
  - name: myadminuser
    groups: sudo
    shell: /bin/bash
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh-authorized-keys:
      - ssh-rsa AAAAB3<snip>
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#create) och ange molnet init-fil med `--custom-data cloud_init_add_user.txt` på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVMUser \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud_init_add_user.txt
```

SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange dina egna offentliga IP-adressen på följande sätt:

```bash
ssh myadminuser@publicIpAddress
```

Bekräfta dina användare har lagts till den virtuella datorn och de angivna grupperna genom att visa innehållet i den */etc/grupp* enligt följande:

```bash
cat /etc/group
```

Följande exempel visas användaren från den *cloud_init_add_user.txt* fil har lagts till den virtuella datorn och den aktuella gruppen:

```bash
root:x:0:
<snip />
sudo:x:27:myadminuser
<snip />
myadminuser:x:1000:
```

## <a name="next-steps"></a>Nästa steg
Molnet init är en standard sätt att ändra din virtuella Linux-datorer på Start. Du kan också använda VM-tillägg för att ändra din Linux VM på Start eller när den körs i Azure. Du kan till exempel använda Virtuella Azure-tillägget för att köra ett skript på en aktiv virtuell dator, inte bara att starta. Mer information om VM-tillägg finns [VM-tillägg och funktioner](extensions-features.md), eller exempel på hur du använder tillägget finns [hantera användare, SSH, och kontrollera eller reparera diskar på virtuella Azure Linux-datorer med hjälp av VMAccess-tillägget](using-vmaccess-extension.md).