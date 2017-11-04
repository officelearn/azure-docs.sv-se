---
title: "Återställa åtkomst till en Azure Linux-dator | Microsoft Docs"
description: "Hur du hanterar administrativa användare och återställa åtkomst på virtuella Linux-datorer med hjälp av VMAccess-tillägget och Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 3596b50b68cabf212218825566c0f8313f054f65
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Hantera administrativa användare, SSH och kontrollera eller reparera diskar på virtuella Linux-datorer med hjälp av VMAccess-tillägget med Azure CLI 2.0
Disken på Linux-VM visas fel. Du på något sätt återställa rotlösenordet för Linux-VM eller tagits bort av misstag din privata SSH-nyckel. Om detta har inträffat i dagar för datacenter, behöver du köra det och öppna sedan KVM få vid servern. Se Azure VMAccess-tillägget som den KVM-växel som gör att du kan använda konsolen för att återställa åtkomst till Linux eller genomför diskunderhåll nivå.

Den här artikeln visar hur du använder Azure VMAccess-tillägget för att kontrollera eller reparera en disk, återställa användaråtkomst, hantera administrativa användarkonton eller återställa SSH-konfigurationen på Linux. Du kan också utföra dessa steg med [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Sätt att använda VMAccess-tillägget
Det finns två sätt som du kan använda VMAccess-tillägget på din virtuella Linux-datorer:

* Använda Azure CLI 2.0 och de obligatoriska parametrarna.
* [Använd raw JSON-filer som bearbetar VMAccess-tillägget](#use-json-files-and-the-vmaccess-extension) och sedan vidta åtgärder för.

I följande exempel används [az vm användaren](/cli/azure/vm/user) kommandon. Om du vill utföra dessa steg behöver du senast [Azure CLI 2.0](/cli/azure/install-az-cli2) installerad och inloggad till en Azure-konto med hjälp av [az inloggningen](/cli/azure/#login).

## <a name="reset-ssh-key"></a>Återställ SSH-nyckel
I följande exempel återställer SSH-nyckeln för användaren `azureuser` på den virtuella datorn med namnet `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Återställa lösenord
I följande exempel återställer lösenordet för användaren `azureuser` på den virtuella datorn med namnet `myVM`:

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Starta om SSH
I följande exempel startar om SSH-daemon och återställer SSH-konfigurationen till standardvärdena på en virtuell dator med namnet `myVM`:

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Skapa en administrativ/sudo-användare
I följande exempel skapas en användare med namnet `myNewUser` med **sudo** behörigheter. Kontot använder en SSH-nyckel för autentisering på den virtuella datorn med namnet `myVM`. Den här metoden är utformad för att hjälpa dig att få åtkomst till en virtuell dator om den aktuella autentiseringsuppgifter tappas bort eller glömmer bort. Som bästa praxis, konton med **sudo** behörigheter ska vara begränsad.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Tar bort en användare
I följande exempel tar bort en användare med namnet `myNewUser` på den virtuella datorn med namnet `myVM`:

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Använd JSON-filer och VMAccess-tillägget
I följande exempel används raw JSON-filer. Använd [az vm-tillägget set](/cli/azure/vm/extension#set) att anropa JSON-filer. Filerna JSON kallas även från Azure-mallar. 

### <a name="reset-user-access"></a>Återställ användaråtkomst
Om du har förlorat åtkomsten till roten på Linux-VM, kan du starta ett VMAccess-skript för att återställa SSH-nyckeln eller lösenordet för en användare.

Om du vill återställa den offentliga SSH-nyckeln för en användare skapar en fil med namnet `reset_ssh_key.json` och lägga till inställningarna i följande format. Ersätt värdena för den `username` och `ssh_key` parametrar:

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Om du vill återställa en användarlösenord, skapar du en fil med namnet `reset_user_password.json` och lägga till inställningarna i följande format. Ersätt värdena för den `username` och `password` parametrar:

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Starta om SSH
Om du vill starta om SSH-daemon och återställa SSH-konfigurationen till standardvärdena, skapar du en fil med namnet `reset_sshd.json`. Lägg till följande innehåll:

```json
{
  "reset_ssh": true
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Hantera administrativa användare

Så här skapar du en användare med **sudo** behörigheter som använder en SSH-nyckel för autentisering, skapa en fil med namnet `create_new_user.json` och lägga till inställningarna i följande format. Ersätt värdena för den `username` och `ssh_key` parametrar. Den här metoden är utformad för att hjälpa dig att få åtkomst till en virtuell dator om den aktuella autentiseringsuppgifter tappas bort eller glömmer bort. Som bästa praxis, konton med **sudo** behörigheter ska vara begränsad.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Ta bort en användare genom att skapa en fil med namnet `delete_user.json` och Lägg till följande innehåll. Ersätt värdet för den `remove_user` parameter:

```json
{
  "remove_user":"myNewUser"
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Kontrollera eller reparera disken
Med hjälp av VMAccess du också kontrollera och reparera en disk som du har lagt till Linux-VM.

Om du vill kontrollera och reparera disken, skapa en fil med namnet `disk_check_repair.json` och lägga till inställningarna i följande format. Ersätt värdet för namnet på `repair_disk`:

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Kör skriptet VMAccess med:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Nästa steg
Uppdatera Linux med hjälp av Azure VMAccess-tillägget är en metod för att göra ändringar på en Linux-VM som körs. Du kan också använda verktyg som molntjänster init och Azure Resource Manager-mallar för att ändra din Linux VM på Start.

[Tillägg för virtuell dator och funktioner för Linux](extensions-features.md)

[Skapa Azure Resource Manager-mallar med Linux VM-tillägg](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Med hjälp av molnet init för att anpassa en Linux VM under skapandet](using-cloud-init.md)

