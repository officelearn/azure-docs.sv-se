---
title: Återställa åtkomst på virtuella Azure Linux-datorer med hjälp av VMAccess-tillägget | Microsoft Docs
description: Återställa åtkomst på virtuella Azure Linux-datorer med hjälp av VMAccess-tillägget.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: v-livech
ms.openlocfilehash: 5fb130fc2e448f3cbc648991ea6bebd5795bc78b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="manage-users-ssh-and-check-or-repair-disks-on-azure-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-10"></a>Hantera användare, SSH och kontrollera eller reparera diskar på virtuella Azure Linux-datorer med Azure CLI 1.0 VMAccess-tillägget
Den här artikeln visar hur du använder Azure VMAcesss tillägget för att kontrollera eller reparera en disk, återställa användaråtkomst, hantera användarkonton eller återställa SSHD konfigurationen på Linux. Artikeln kräver:

* ett Azure-konto ([hämta en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)).
* [Azure CLI](../../cli-install-nodejs.md) inloggad med `azure login`.
* Azure CLI *måste vara i* Azure Resource Manager-läge `azure config mode arm`.


## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- [Azure CLI 1.0](#quick-commands)– våra CLI för klassisk och resurs management på distributionsmodeller (den här artikeln)
- [Azure CLI 2.0](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – vår nästa generations CLI för distributionsmodellen resurshantering


## <a name="quick-commands"></a>Snabbkommandon
Det finns två sätt att använda VMAccess på din virtuella Linux-datorer:

* Med hjälp av Azure CLI 1.0 och de obligatoriska parametrarna.
* Använder rådata JSON-filer som VMAccess bearbetar och sedan vidta åtgärder för.

För avsnittet snabbkommando vi ska använda Azure CLI 1.0 `azure vm reset-access` metod. I följande kommandoexempel ersätter du värdena som innehåller ”exempel” med värden från din egen miljö.

## <a name="create-a-resource-group-and-linux-vm"></a>Skapa en resursgrupp och en virtuell Linux-dator
```bash
azure group create myResourceGroup westus
```

## <a name="create-a-debian-vm"></a>Skapa en Debian virtuell dator
```azurecli
azure vm quick-create \
  -M ~/.ssh/id_rsa.pub \
  -u myAdminUser \
  -g myResourceGroup \
  -l westus \
  -y Linux \
  -n myVM \
  -Q Debian
```

## <a name="reset-root-password"></a>Återställ rotlösenordet
Så här återställer rotlösenordet:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u root \
  -p myNewPassword
```

## <a name="ssh-key-reset"></a>Återställning av SSH-nyckel
Att återställa SSH-nyckeln för en rot-användare:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -M ~/.ssh/id_rsa.pub
```

## <a name="create-a-user"></a>Skapa en användare
Så här skapar du en användare:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -u myAdminUser \
  -p myAdminUserPassword
```

## <a name="remove-a-user"></a>Ta bort en användare
```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM \
  -R myRemovedUser
```

## <a name="reset-sshd"></a>Återställ SSHD
Att återställa SSHD konfigurationen:

```azurecli
azure vm reset-access \
  -g myResourceGroup \
  -n myVM
  -r
```


## <a name="detailed-walkthrough"></a>Detaljerad genomgång
### <a name="vmaccess-defined"></a>VMAccess definierats:
Disken på Linux-VM visas fel. Du på något sätt återställa rotlösenordet för Linux-VM eller tagits bort av misstag din privata SSH-nyckel. Om detta har inträffat i dagar för datacenter, behöver du köra det och öppna sedan KVM få vid servern. Se Azure VMAccess-tillägget som den KVM-växel som gör att du kan använda konsolen för att återställa åtkomst till Linux eller genomför diskunderhåll nivå.

Detaljerad genomgång ska vi använda lång form av VMAccess som använder rådata JSON-filer.  Filerna VMAccess JSON kallas även från Azure-mallar.

### <a name="using-vmaccess-to-check-or-repair-the-disk-of-a-linux-vm"></a>Använda VMAccess för att kontrollera eller reparera disken för en Linux-VM
Med hjälp av VMAccess kan du göra en fsck kör på disken under Linux-VM.  Du kan också göra en kontroll och en diskreparationen med hjälp av en VMAccess.

Om du vill kontrollera och reparera sedan använda disken VMAccess skriptet:

`disk_check_repair.json`

```json
{
  "check_disk": "true",
  "repair_disk": "true, user-disk-name"
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path disk_check_repair.json
```

### <a name="using-vmaccess-to-reset-user-access-to-linux"></a>Använda VMAccess för att återställa användaråtkomst till Linux
Om du har förlorat åtkomsten till roten på Linux-VM, kan du starta ett VMAccess-skript för att återställa rotlösenordet.

Använd det här VMAccess-skriptet för att återställa rotlösenordet:

`reset_root_password.json`

```json
{
  "username":"root",
  "password":"myNewPassword"
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_root_password.json
```

Använd det här VMAccess-skriptet för att återställa SSH-nyckeln för en rotanvändare:

`reset_ssh_key.json`

```json
{
  "username":"myAdminUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myAdminUser@myVM" 
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_ssh_key.json
```

### <a name="using-vmaccess-to-manage-user-accounts-on-linux"></a>Hantera användarkonton i Linux med hjälp av VMAccess
VMAccess är en Python-skript som kan användas för att hantera användare på Linux-VM utan att logga in och använda sudo eller rotkontot.

Använd VMAccess skriptet för att skapa en användare:

`create_new_user.json`

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path create_new_user.json
```

Använd det här VMAccess-skriptet för att ta bort en användare:

`remove_user.json`

```json
{
  "remove_user":"myDeletedUser"
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path remove_user.json
```

### <a name="using-vmaccess-to-reset-the-sshd-configuration"></a>Med hjälp av VMAccess för att återställa SSHD konfigurationen
Om du gör ändringar i konfigurationen för Linux virtuella datorer SSHD och stänger SSH-anslutningen innan du kontrollerar ändringarna du kan förhindras att SSH'ing igen.  VMAccess kan användas för att återställa SSHD konfiguration till en fungerande konfiguration utan loggas via SSH.

Om du vill återställa använda konfigurationen SSHD VMAccess skriptet:

`reset_sshd.json`

```json
{
  "reset_ssh": true
}
```

Kör skriptet VMAccess med:

```azurecli
azure vm extension set \
  myResourceGroup \
  myVM \
  VMAccessForLinux \
  Microsoft.OSTCExtensions * \
  --private-config-path reset_sshd.json
```

## <a name="next-steps"></a>Nästa steg
Uppdatera Linux är med hjälp av Azure VMAccess tillägg en metod för att göra ändringar på en Linux-VM som körs.  Du kan också använda verktyg som molntjänster init och Azure-mallar för att ändra din Linux VM på Start.

[Om virtuella datortillägg och funktioner](../windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Skapa Azure Resource Manager-mallar med Linux VM-tillägg](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Med hjälp av molnet init för att anpassa en Linux VM under skapandet](using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

