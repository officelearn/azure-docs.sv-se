---
title: Förbereda Azure VM-avbildning för användning med cloud-init
description: Förbereda en befintlig Azure VM-avbildning för distribution med cloud-init
author: danis
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: danis
ms.openlocfilehash: fef41f4dc90c03e3efbe4c8a75e495c26eec64b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066823"
---
# <a name="prepare-an-existing-linux-azure-vm-image-for-use-with-cloud-init"></a>Förbereda en befintlig Linux Azure VM-avbildning för användning med cloud-init
Den här artikeln visar hur du tar en befintlig virtuell Azure-dator och förbereder den för att distribueras om och är klar att använda cloud-init. Den resulterande avbildningen kan användas för att distribuera en ny virtuell dator eller skalningsuppsättningar för virtuella datorer – varav någon kan sedan anpassas ytterligare av cloud-init vid distributionen.  Dessa cloud-init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur cloud-init fungerar inbyggt i Azure och de [Linux-distributioner](using-cloud-init.md) som stöds finns i översikt över molnet

## <a name="prerequisites"></a>Krav
Det här dokumentet förutsätter att du redan har en azure-virtuell dator som kör en version av Operativsystemet Linux som stöds. Du har redan konfigurerat maskinen så att den passar dina behov, installerat alla nödvändiga moduler, bearbetat alla nödvändiga uppdateringar och testat den för att säkerställa att den uppfyller dina krav. 

## <a name="preparing-rhel-76--centos-76"></a>Förbereda RHEL 7,6 / CentOS 7,6
Du måste SSH i din Virtuella Linux-dator och köra följande kommandon för att installera cloud-init.

```bash
sudo yum makecache fast
sudo yum install -y gdisk cloud-utils-growpart
sudo yum install - y cloud-init 
```

Uppdatera `cloud_init_modules` avsnittet `/etc/cloud/cloud.cfg` så att det innehåller följande moduler:

```bash
- disk_setup
- mounts
```

Här är ett exempel på `cloud_init_modules` hur ett allmänt avsnitt ser ut.

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

Ett antal uppgifter som rör etablering och hantering av efemära `/etc/waagent.conf`diskar måste uppdateras i . Kör följande kommandon för att uppdatera lämpliga inställningar.

```bash
sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
cloud-init clean
```

Tillåt endast Azure som en datakälla för Azure `/etc/cloud/cloud.cfg.d/91-azure_datasource.cfg` Linux Agent genom att skapa en ny fil med hjälp av en redigerare som du väljer med följande rad:

```bash
# Azure Data Source config
datasource_list: [ Azure ]
```

Om din befintliga Azure-avbildning har konfigurerat en växlingsfil och du vill ändra konfigurationen för växlingsfilen för nya avbildningar med moln-init måste du ta bort den befintliga växlingsfilen.

För Red Hat-baserade bilder - följ instruktionerna i följande Red Hat-dokument som förklarar hur du [tar bort växlingsfilen](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/swap-removing-file).

För CentOS-bilder med swapfile aktiverat kan du köra följande kommando för att stänga av växlingsfilen:

```bash
sudo swapoff /mnt/resource/swapfile
```

Se till att swapfile `/etc/fstab` referens tas bort från - det bör se ut ungefär så här ut:

```output
# /etc/fstab
# Accessible filesystems, by reference, are maintained under '/dev/disk'
# See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
#
UUID=99cf66df-2fef-4aad-b226-382883643a1c / xfs defaults 0 0
UUID=7c473048-a4e7-4908-bad3-a9be22e9d37d /boot xfs defaults 0 0
```

Så här sparar du utrymme och tar bort växlingsfilen kan du köra följande kommando:

```bash
rm /mnt/resource/swapfile
```

## <a name="extra-step-for-cloud-init-prepared-image"></a>Extra steg för moln-init förberedd bild
> [!NOTE]
> Om avbildningen tidigare var en **moln-init förberedd** och konfigurerad avbildning måste du göra följande steg.

Följande tre kommandon används endast om den virtuella datorn som du anpassar till att vara en ny specialiserad källavbildning tidigare har etablerats av cloud-init.  Du behöver INTE köra dessa om avbildningen har konfigurerats med Azure Linux Agent.

```bash
sudo cloud-init clean --logs
sudo waagent -deprovision+user -force
```

## <a name="finalizing-linux-agent-setting"></a>Slutföra Linux-agentinställning 
Alla Azure-plattformsavbildningar har Azure Linux-agenten installerad, oavsett om den har konfigurerats av cloud-init eller inte.  Kör följande kommando för att slutföra avetablering av användaren från Linux-datorn. 

```bash
sudo waagent -deprovision+user -force
```

Mer information om Azure Linux Agent-avetableringar finns i [Azure Linux Agent](../extensions/agent-linux.md) för mer information.

Avsluta SSH-sessionen och kör sedan följande AzureCLI-kommandon från ditt bash-skal för att frigöra, generalisera och skapa en ny Azure VM-avbildning.  Ersätt `myResourceGroup` `sourceVmName` och med lämplig information som återspeglar din sourceVM.

```azurecli
az vm deallocate --resource-group myResourceGroup --name sourceVmName
az vm generalize --resource-group myResourceGroup --name sourceVmName
az image create --resource-group myResourceGroup --name myCloudInitImage --source sourceVmName
```

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
