---
title: Använda cloud-init för att köra ett bash-skript i en Virtuell Linux-dator på Azure
description: Så här använder du cloud-init för att köra ett bash-skript i en Virtuell Linux-dator när du skapar med Azure CLI
author: rickstercdn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e2f19ceb6c7f19ba749b46a3553036587be6a71a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969219"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Använda cloud-init för att köra ett bash-skript i en Virtuell Linux-dator i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att köra ett befintligt bash-skript på en virtuell Linux-dator (VM) eller vmss (virtual machine scale sets) vid etableringstid i Azure. Dessa cloud-init-skript körs vid första start när resurserna har etablerats av Azure. Mer information om hur cloud-init fungerar inbyggt i Azure och de [Linux-distributioner](using-cloud-init.md) som stöds finns i översikt över molnet

## <a name="run-a-bash-script-with-cloud-init"></a>Kör ett bash-skript med cloud-init
Med cloud-init behöver du inte konvertera dina befintliga skript till en moln-config, cloud-init accepterar flera indatatyper, varav en är en bash script.

Om du har använt Azure-tillägget för anpassat skript för att köra skripten kan du migrera dem för att använda cloud-init. Azure-tillägg har dock integrerad rapportering för att avisera skriptfel, en moln-init-avbildningsdistribution misslyckas inte om skriptet misslyckas.

Om du vill se den här funktionen i praktiken skapar du ett enkelt bash-skript för testning. Precis som molninitfilen `#cloud-config` måste skriptet vara lokalt där du kör AzureCLI-kommandona för att etablera din virtuella dator.  I det här exemplet skapar du filen i Cloud Shell som inte finns på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor simple_bash.sh` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att **nano** använda nanoredigeraren. Kontrollera att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med kommandot [az group create.](/cli/azure/group) En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu, skapa en virtuell dator med az vm `--custom-data simple_bash.sh` [skapa](/cli/azure/vm) och ange bash script-filen med följande:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verifiera bash-skriptet har körts
SSH till den offentliga IP-adressen för den virtuella datorn som visas i utdata från föregående kommando. Ange din egen **publicIpAddress** enligt följande:

```bash
ssh <publicIpAddress>
```

Ändra till **katalogen /tmp** och kontrollera att filen myScript.txt finns och har lämplig text inuti den.  Om den inte gör det kan du kontrollera **/var/log/cloud-init.log** för mer information.  Sök efter följande post:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Nästa steg
Ytterligare moln-init-exempel på konfigurationsändringar finns i följande avsnitt:
 
- [Lägga till ytterligare en Linux-användare i en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra virtuellt lokalt värdnamn](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och injicera nycklar](tutorial-automate-vm-deployment.md)
