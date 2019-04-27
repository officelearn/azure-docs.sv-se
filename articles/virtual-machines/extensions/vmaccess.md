---
title: Återställa åtkomst till en virtuell Linux-dator | Microsoft Docs
description: Hur du hanterar administrativa användare och Återställ åtkomst på virtuella Linux-datorer med hjälp av VMAccess-tillägget och Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2018
ms.author: roiyz
ms.openlocfilehash: 71aecc1748e70e2119b1f54c21a0f705afc5d5d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800057"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Hantera administrativa användare, SSH och kontrollera eller reparera diskar på virtuella Linux-datorer med hjälp av VMAccess-tillägget med Azure CLI
## <a name="overview"></a>Översikt
Disken på din Linux-VM visar fel. Du på något sätt återställa rotlösenordet för din Linux-VM eller ta bort SSH-privata nyckeln av misstag. Om detta har inträffat i dagar i datacentret, skulle du behöva öka det och öppna sedan KVM att hämta vid servern. Tänk på Azure VMAccess-tillägget som denna KVM-växel som gör det möjligt att få åtkomst till konsolen för att återställa åtkomst till Linux eller genomför diskunderhåll nivå.

Den här artikeln visar hur du använder Azure VMAccess-tillägget för att kontrollera eller reparera en disk, Återställ användarnas åtkomst, hantera administrativa användarkonton eller uppdatera SSH-konfigurationen på Linux när de körs som Azure Resource Manager-datorer. Om du behöver hantera klassiska virtuella datorer – du kan följa instruktionerna i den [dokumentationen för klassiska virtuella datorer](../linux/classic/reset-access-classic.md). 
 
> [!NOTE]
> Om du använder VMAccess-tillägget för att återställa lösenordet för den virtuella datorn när du har installerat tillägget AAD-inloggning måste du köra tillägget AAD-inloggning om du vill återaktivera AAD-inloggning för din dator.

## <a name="prerequisites"></a>Nödvändiga komponenter
### <a name="operating-system"></a>Operativsystem

VM Access-tillägget kan köras mot dessa Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS och 12.04 LTS |
| Debian | Debian 7.9+, 8.2+ |
| Red Hat | RHEL 6.7+, 7.1+ |
| Oracle Linux | 6.4+, 7.0+ |
| SUSE | 11 och 12 |
| OpenSuse | openSUSE steg 42.2 + |
| CentOS | CentOS 6.3+, 7.0+ |
| CoreOS | 494.4.0+ |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sätt att använda VMAccess-tillägget
Det finns två sätt som du kan använda VMAccess-tillägget på din virtuella Linux-datorer:

* Använd Azure CLI och de obligatoriska parametrarna.
* [Använda raw JSON-filer som VMAccess-tillägget bearbetar](#use-json-files-and-the-vmaccess-extension) och sedan agera utifrån.

I följande exempel används [az vm user](/cli/azure/vm/user) kommandon. Om du vill utföra dessa steg du behöver senast [Azure CLI](/cli/azure/install-az-cli2) installerat och loggat in till en Azure-konto med hjälp av [az-inloggning](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Uppdatera SSH-nyckel
I följande exempel uppdateras SSH-nyckel för användaren `azureuser` på den virtuella datorn med namnet `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Obs!** Den `az vm user update` kommando lägger du till den nya offentliga nyckel texten ska den `~/.ssh/authorized_keys` fil för administratören på den virtuella datorn. Detta inte ersätter eller ta bort några befintliga SSH-nycklar. Detta tar inte bort tidigare nycklar som anges vid tidpunkten för distribution eller efterföljande uppdateringar via VMAccess-tillägget.

## <a name="reset-password"></a>Återställa lösenord
I följande exempel återställer lösenordet för användaren `azureuser` på den virtuella datorn med namnet `myVM`:

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Starta om SSH
I följande exempel startar om SSH-daemon och återställer SSH-konfigurationen till standardvärden på en virtuell dator med namnet `myVM`:

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Skapar du en administrativ/sudo-användare
I följande exempel skapas en användare med namnet `myNewUser` med **sudo** behörigheter. Konton som använder en SSH-nyckel för autentisering på den virtuella datorn med namnet `myVM`. Den här metoden är avsedd för att få åtkomst till en virtuell dator om den aktuella autentiseringsuppgifter har förlorat. Som bästa praxis-konton med **sudo** behörigheter ska vara begränsad.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Ta bort en användare
I följande exempel tar bort en användare med namnet `myNewUser` på den virtuella datorn med namnet `myVM`:

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Använda JSON-filer och VMAccess-tillägget
I följande exempel används raw JSON-filer. Använd [az vm-tilläggsuppsättningen](/cli/azure/vm/extension) att anropa JSON-filer. De här JSON-filer kan också anropas från Azure-mallar. 

### <a name="reset-user-access"></a>Återställ användaråtkomst
Om du har förlorat åtkomsten till rot på Linux-VM, kan du starta en VMAccess-skript för att uppdatera en användares SSH-nyckel eller lösenord.

Om du vill uppdatera den offentliga SSH-nyckeln för en användare skapar du en fil med namnet `update_ssh_key.json` och lägga till inställningar i följande format. Ersätt värdena för den `username` och `ssh_key` parametrar:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Kör skriptet VMAccess med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Om du vill återställa en användarlösenord, skapa en fil med namnet `reset_user_password.json` och lägga till inställningar i följande format. Ersätt värdena för den `username` och `password` parametrar:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Kör skriptet VMAccess med:

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
Om du vill starta om daemon för SSH och återställa SSH-konfigurationen till standardvärden, skapa en fil med namnet `reset_sshd.json`. Lägg till följande innehåll:

```json
{
  "reset_ssh": true
}
```

Kör skriptet VMAccess med:

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

Skapa en användare med **sudo** behörigheter som använder en SSH-nyckel för autentisering, skapa en fil med namnet `create_new_user.json` och lägga till inställningar i följande format. Ersätt värdena för den `username` och `ssh_key` parametrar. Den här metoden är avsedd för att få åtkomst till en virtuell dator om den aktuella autentiseringsuppgifter har förlorat. Som bästa praxis-konton med **sudo** behörigheter ska vara begränsad.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Kör skriptet VMAccess med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Ta bort en användare genom att skapa en fil med namnet `delete_user.json` och Lägg till följande innehåll. Ersätt ditt eget värde för den `remove_user` parameter:

```json
{
  "remove_user":"myNewUser"
}
```

Kör skriptet VMAccess med:

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
Med hjälp av VMAccess du också kontrollera och reparera en disk som du lade till Linux VM.

Om du vill kontrollera och reparera disken, skapa en fil med namnet `disk_check_repair.json` och lägga till inställningar i följande format. Ersätt ditt eget värde för namnet på `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Kör skriptet VMAccess med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
