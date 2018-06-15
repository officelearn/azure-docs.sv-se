---
title: Förbereda Azure VM-avbildning för användning med molnet init | Microsoft Docs
description: Hur du förbereder en befintlig virtuell dator i Azure-avbildning för distribution med molnet initiering
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
ms.openlocfilehash: faa28a6b28c721e4088ccfbb00514be7f605f3e2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33943754"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Förbereda en befintlig Linux Azure VM-avbildning för användning med molnet initiering
Den här artikeln visar hur du tar en befintlig Azure virtuell dator och förbereda den omdistribuerade och klar att använda molnet initiering. Bilden kan användas för att distribuera en ny virtuell dator eller skalningsuppsättningar i virtuella - som kan sedan anpassas ytterligare genom molnet init vid tidpunkten för distribution.  Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="prerequisites"></a>Förutsättningar
Det här dokumentet förutsätter att du redan har en körs virtuella Azure-datorn kör en version av Linux-operativsystem som stöds. Du redan har konfigurerat datorn så att de passar dina behov, installeras alla moduler som krävs, bearbetas alla nödvändiga uppdateringar och har testat den för att kontrollera att den uppfyller dina krav. 

## <a name="preparing-rhel-74--centos-74"></a>Förbereda RHEL 7.4 / CentOS 7.4
Du behöver SSH till din Linux VM och kör följande kommandon för att kunna installera molnet initiering.

```bash
sudo yum install -y cloud-init gdisk
sudo yum check-update cloud-init -y
sudo yum install cloud-init -y
```

Uppdatering av `cloud_init_modules` i avsnittet `/etc/cloud/cloud.cfg` att inkludera följande moduler:
```bash
- disk_setup
- mounts
```

Här är ett exempel på vilka allmänna `cloud_init_modules` avsnitt ser ut.
```bash
cloud_init_modules:
 - migrator
 - bootcmd
 - write-files
 - growpart
 - resizefs
 - disk_setup
 - mounts
 - set_hostname
 - update_hostname
 - update_etc_hosts
 - rsyslog
 - users-groups
 - ssh
```
Ett antal åtgärder som rör etablering och hantering av tillfälliga diskar som behöver uppdateras i `/etc/waagent.conf`. Kör följande kommandon för att uppdatera inställningarna. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
```
Tillåt endast Azure som en datakälla för Azure Linux-agenten genom att skapa en ny fil `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` med hjälp av ett redigeringsprogram med följande rader:

```bash
# This configuration file is provided by the WALinuxAgent package.
datasource_list: [ Azure ]
```

Lägga till en konfiguration för att åtgärda en utestående hostname registrering programfel.
```bash
cat > /etc/cloud/hostnamectl-wrapper.sh <<\EOF
#!/bin/bash -e
if [[ -n $1 ]]; then
  hostnamectl set-hostname $1
else
  hostname
fi
EOF

chmod 0755 /etc/cloud/hostnamectl-wrapper.sh

cat > /etc/cloud/cloud.cfg.d/90-hostnamectl-workaround-azure.cfg <<EOF
# local fix to ensure hostname is registered
datasource:
  Azure:
    hostname_bounce:
      hostname_command: /etc/cloud/hostnamectl-wrapper.sh
EOF
```

Om din befintlig Azure-avbildning har en växlingsfil konfigurerats och du vill ändra konfigurationen för växlingsutrymme-filen för nya avbildningar med molnet init, måste du ta bort befintliga växlingsfilen.

För RedHat baserad bilder - följer du anvisningarna i den följande RedHat dokument som förklarar hur du [ta bort växlingsfilen](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/5/html/Deployment_Guide/s2-swap-removing-file.html).

Du kan köra följande kommando för att stänga av växlingsfil för CentOS bilder med växlingsfil aktiverad:
```bash
sudo swapoff /mnt/resource/swapfile
```

Växlingsfil-referens tas bort från `/etc/fstab` -det bör se ut ungefär som följande utdata:
```text
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Du kan köra följande kommando för att spara utrymme och ta bort växlingsfilen:
```bash
rm /mnt/resource/swapfile
```
## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra steg för molnet init förberedda avbildningen
> [!NOTE]
> Om avbildningen tidigare var en **moln init** förberedda och konfigurerade bilden, behöver du följande steg.

Följande tre kommandon används bara om den virtuella datorn du anpassar så att en ny specialiserade Källavbildningen tidigare har etablerats av molnet initiering.  Du behöver inte köra dessa om bilden har konfigurerats med hjälp av Azure Linux-agenten.

```bash
sudo rm -rf /var/lib/cloud/instances/* 
sudo rm -rf /var/log/cloud-init*
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Slutför Linux-agenten inställningen 
Alla avbildningar i Azure-plattformen har Azure Linux-agenten installerad, oavsett om den har konfigurerats av molnet init eller inte.  Kör följande kommando för att slutföra avetablering användaren från Linux-dator. 

```bash
sudo waagent -deprovision+user -force
```

Mer information om Azure Linux-agenten avetablering kommandon finns på [Azure Linux-agenten](../extensions/agent-linux.md) för mer information.

Avsluta SSH-session från din bash shell och kör sedan följande kommandon för AzureCLI att frigöra, generalisera och skapa en ny virtuell dator i Azure-avbildning.  Ersätt `myResourceGroup` och `sourceVmName` med lämplig information reflektion din sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
