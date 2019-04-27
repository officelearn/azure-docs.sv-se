---
title: Förbereda Azure VM-avbildning för användning med cloud-init | Microsoft Docs
description: Hur du förbereder en befintlig Azure VM-avbildning för distribution med cloud-init
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 02/27/2019
ms.author: danis
ms.openlocfilehash: da539a5bebc1613115f89a7b47c513ce486b5e3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627945"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Förbereda en befintlig Linux Azure VM-avbildning för användning med cloud-init
Den här artikeln visar hur du tar en befintlig Azure virtuell dator och förbereda den för att vara omdistribuerade och redo att använda cloud-init. Bilden kan användas för att distribuera en ny virtuell dator eller VM-skalningsuppsättningar – vilket kan sedan anpassas ytterligare genom cloud-init vid tidpunkten för distribution.  Skripten cloud-init körs vid den första starten när resurserna har etablerats med Azure. Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [cloud-init-översikt](using-cloud-init.md)

## <a name="prerequisites"></a>Nödvändiga komponenter
Det här dokumentet förutsätter att du redan har en aktiva Azure virtuell dator som kör en version av Linux-operativsystem som stöds. Du redan har konfigurerat datorn så att den passar dina behov, installeras alla moduler som krävs, bearbetas alla nödvändiga uppdateringar och har testat den för att säkerställa att den uppfyller dina krav. 

## <a name="preparing-rhel-76--centos-76"></a>Förbereda RHEL 7.6 / CentOS 7.6
Du behöver SSH till din Linux-VM och kör följande kommandon för att kunna installera cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Uppdatera den `cloud_init_modules` i avsnittet `/etc/cloud/cloud.cfg` att inkludera följande moduler:
```bash
- disk_setup
- mounts
```

Här följer ett exempel på vilka allmänna `cloud_init_modules` avsnittet se ut.
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
Ett antal uppgifter som rör etablering och hantering av differentierande diskar behöver uppdateras i `/etc/waagent.conf`. Kör följande kommandon för att uppdatera inställningarna. 
```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cp /lib/systemd/system/waagent.service /etc/systemd/system/waagent.service
sed -i 's/After=network-online.target/WantedBy=cloud-init.service\\nAfter=network.service systemd-networkd-wait-online.service/g' /etc/systemd/system/waagent.service
systemctl daemon-reload
cloud-init clean
```
Tillåt endast Azure som en datakälla för Azure Linux Agent genom att skapa en ny fil `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` med hjälp av ett redigeringsprogram med följande rader:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
datasource:
   Azure:
     agent_command: [systemctl, start, waagent, --no-block]
```

Om din befintliga Azure avbildningen har en växlingsfil som konfigurerats och du vill ändra konfigurationen för swap-fil för nya avbildningar med cloud-init, måste du ta bort befintliga växlingsfilen.

För Red Hat-baserade avbildningar - följer du anvisningarna i den följande förklarar för Red Hat dokumentet så [ta bort växlingsfilen](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

CentOS-avbildningar med växlingsfil aktiverat kan köra du följande kommando för att stänga av växlingsfil:
```bash
sudo swapoff /mnt/resource/swapfile
```

Referens för växlingsfil tas bort från `/etc/fstab` -det bör likna följande utdata:
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
## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra steg för cloud-init förberett bild
> [!NOTE]
> Om din avbildning tidigare var en **cloud-init** förberedd och förkonfigurerad avbildning, behöver du följande steg.

Följande tre kommandon används bara om den virtuella datorn som du anpassar för att vara en ny specialiserade källbild etablerades tidigare med cloud-init.  Du behöver inte köra dessa om avbildningen har konfigurerats med hjälp av Azure Linux Agent.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Slutför Linux-agenten inställningen 
Alla avbildningar i Azure-plattformen har Azure Linux Agent installerad, oavsett om den har konfigurerats med cloud-init eller inte.  Kör följande kommando för att slutföra avetablering användaren från Linux-dator. 

```bash
sudo waagent -deprovision+user -force
```

Mer information om Azure Linux Agent avetablering kommandon finns i den [Azure Linux Agent](../extensions/agent-linux.md) för mer information.

Avsluta SSH-sessionen från bash-gränssnitt och kör sedan följande AzureCLI-kommandon för att frigöra, generalisera och skapa en ny Azure VM-avbildning.  Ersätt `myResourceGroup` och `sourceVmName` med lämplig information som uppfyller dina sourceVM.

```bash
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
