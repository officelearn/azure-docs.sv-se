---
title: Återställa åtkomsten till en virtuell Azure Linux-dator
description: Hantera administrativa användare och återställa åtkomsten på virtuella Linux-datorer med VMAccess-tillägget och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: akjosh
ms.openlocfilehash: bd9dc05a84a4ee54fce40e6c88e87ac90bfee8a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250366"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Hantera administrativa användare, SSH och kontrollera eller reparera diskar på virtuella Linux-datorer med VMAccess-tillägget med Azure CLI
## <a name="overview"></a>Översikt
Disken på din virtuella Linux-dator visar fel. Du återställer på något sätt rotlösenordet för din Virtuella Linux-dator eller raderade din SSH-privata nyckel av misstag. Om det hände redan i dagarna av datacentret, skulle du behöva köra dit och sedan öppna KVM för att komma åt serverkonsolen. Tänk på Azure VMAccess-tillägget som den KVM-växel som gör att du kan komma åt konsolen för att återställa åtkomsten till Linux eller utföra disknivåunderhåll.

Den här artikeln visar hur du använder Azure VMAccess Extension för att kontrollera eller reparera en disk, återställa användaråtkomst, hantera administrativa användarkonton eller uppdatera SSH-konfigurationen på Linux när de körs som virtuella Azure Resource Manager-datorer. Om du behöver hantera klassiska virtuella datorer - du kan följa instruktionerna som finns i den [klassiska VM-dokumentationen](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Om du använder VMAccess-tillägget för att återställa lösenordet för din virtuella dator efter att ha installerat AAD-inloggningstillägget måste du köra AAD-inloggningstillägget igen för att återaktivera AAD-inloggning för din dator.

## <a name="prerequisites"></a>Krav
### <a name="operating-system"></a>Operativsystem

VM Access-tillägget kan köras mot dessa Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS och 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6,7+, 7,1+ |
| Oracle Linux | 6,4+, 7,0+ |
| Suse | 11 och 12 |
| Opensuse | openSUSE Leap 42.2+ |
| CentOS | CentOS 6,3+, 7,0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sätt att använda VMAccess-tillägget
Det finns två sätt att använda VMAccess-tillägget på dina virtuella Linux-datorer:

* Använd Azure CLI och de parametrar som krävs.
* [Använd råa JSON-filer som VMAccess-tillägget sprocessen](#use-json-files-and-the-vmaccess-extension) och sedan agera på.

I följande exempel används [az vm-användarkommandon.](/cli/azure/vm/user) För att kunna utföra dessa steg behöver du den senaste [Azure CLI](/cli/azure/install-az-cli2) installerad och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Uppdatera SSH-nyckel
I följande exempel uppdateras SSH-nyckeln för användaren `azureuser` på den virtuella datorn med namnet: `myVM`

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **OBS:** Kommandot `az vm user update` lägger till den nya offentliga `~/.ssh/authorized_keys` nyckeltexten i filen för administratörsanvändaren på den virtuella datorn. Detta ersätter eller tar inte bort några befintliga SSH-nycklar. Detta tar inte bort tidigare nycklar som ställts in vid distributionen eller efterföljande uppdateringar via VMAccess-tillägget.

## <a name="reset-password"></a>Återställa lösenord
I följande exempel återställs lösenordet `azureuser` för användaren `myVM`på den virtuella datorn med namnet:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Starta om SSH
I följande exempel startas SSH-demonen om och SSH-konfigurationen `myVM`återställs till standardvärden på en virtuell dator med namnet:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Skapa en administrativ/sudo-användare
I följande exempel skapas `myNewUser` en användare med namnet sudobehörighet. **sudo** Kontot använder en SSH-nyckel för `myVM`autentisering på den virtuella datorn som heter . Den här metoden är utformad för att hjälpa dig att återfå åtkomsten till en virtuell dator i händelse av att aktuella autentiseringsuppgifter försvinner eller glöms bort. Som bästa praxis bör konton med **sudo-behörigheter** begränsas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Ta bort en användare
I följande exempel tas `myNewUser` en användare `myVM`med namnet på den virtuella datorn bort:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Använda JSON-filer och VMAccess-tillägget
I följande exempel används råa JSON-filer. Använd [az vm-tillägget inställt](/cli/azure/vm/extension) på att sedan anropa dina JSON-filer. Dessa JSON-filer kan också anropas från Azure-mallar. 

### <a name="reset-user-access"></a>Återställa användaråtkomst
Om du har förlorat åtkomsten till root på din Virtuella Linux-dator kan du starta ett VMAccess-skript för att uppdatera en användares SSH-nyckel eller lösenord.

Om du vill uppdatera den offentliga SSH-nyckeln för en användare skapar du en fil med namnet `update_ssh_key.json` och lägger till inställningar i följande format. Ersätt dina egna `username` värden `ssh_key` med parametrarna och:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Om du vill återställa ett `reset_user_password.json` användarlösenord skapar du en fil med namnet och lägger till inställningar i följande format. Ersätt dina egna `username` värden `password` med parametrarna och:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Starta om SSH
Om du vill starta om SSH-demonen och återställa SSH-konfigurationen till standardvärden skapar du en fil med namnet `reset_sshd.json`. Lägg till följande innehåll:

```json
{
  "reset_ssh": true
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Hantera administrativa användare

Om du vill skapa en användare med **sudo-behörigheter** som `create_new_user.json` använder en SSH-nyckel för autentisering skapar du en fil med namnet och lägger till inställningar i följande format. Ersätt dina egna `username` värden `ssh_key` för parametrarna och. Den här metoden är utformad för att hjälpa dig att återfå åtkomsten till en virtuell dator i händelse av att aktuella autentiseringsuppgifter försvinner eller glöms bort. Som bästa praxis bör konton med **sudo-behörigheter** begränsas.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Om du vill ta bort `delete_user.json` en användare skapar du en fil med namnet och lägger till följande innehåll. Ersätt ditt eget `remove_user` värde med parametern:

```json
{
  "remove_user":"myNewUser"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Kontrollera eller reparera disken
Med VMAccess kan du också kontrollera och reparera en disk som du har lagt till i Linux-vm.

Om du vill kontrollera och sedan `disk_check_repair.json` reparera disken skapar du en fil med namnet och lägger till inställningar i följande format. Ersätt ditt eget värde `repair_disk`med namnet på:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
