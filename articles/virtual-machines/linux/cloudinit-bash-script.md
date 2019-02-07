---
title: Använda cloud-init för att köra ett bash-skript i en Linux-VM på Azure | Microsoft Docs
description: Hur du använder cloud-init för att köra ett bash-skript i en Linux VM när skapas med Azure CLI
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
ms.openlocfilehash: 4f65ebfd2e1ce508c5cf9b224871102a35b55fe0
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770334"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Använda cloud-init för att köra ett bash-skript i en Linux-VM i Azure
Den här artikeln visar hur du använder [cloud-init](https://cloudinit.readthedocs.io) för att köra en befintlig bash-skript på en Linux-dator (VM) eller virtuell dator av skalningsuppsättningar (VMSS) etableringstid i Azure. Skripten cloud-init körs vid den första starten när resurserna har etablerats med Azure. Mer information om hur cloud-init fungerar internt i Azure och Linux-distributioner som stöds finns i [cloud-init-översikt](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Köra ett bash-skript med cloud-init
Med cloud-init du inte behöver konvertera din befintliga skript till en molnkonfiguration accepterar cloud-init flera indatatyper, varav är ett bash-skript.

Om du har använt Azure tillägget för Linux anpassat skript för att köra dina skript, kan du migrera dem för att använda cloud-init. Dock Azure-tillägg har integrerat rapporterar till aviseringen skriptfel, en cloud-init-avbildningsdistributionen kan inte misslyckas om skriptet misslyckas.

Skapa en enkel bash-skript för att testa om du vill se den här funktionen fungerar i praktiken. Som cloud-init `#cloud-config` filen, det här skriptet måste vara lokal där du ska köra AzureCLI-kommandon för att etablera den virtuella datorn.  I det här exemplet skapar du filen i Cloud Shell inte på den lokala datorn. Du kan använda vilket redigeringsprogram som helst. Ange `sensible-editor simple_bash.sh` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 för att använda den **nano** redigeraren. Se till att hela cloud-init-filen kopieras korrekt, särskilt den första raden.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group) kommando. En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu skapar du en virtuell dator med [az vm skapa](/cli/azure/vm) och ange bash-skriptfil med `--custom-data simple_bash.sh` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Kontrollera bash-skript har körts
SSH till den offentliga IP-adressen för den virtuella datorn visas i utdata från föregående kommando. Ange ett eget **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Ändra till den **/tmp** katalogen och kontrollera myScript.txt filen finns och har den aktuella texten inuti den.  Om det inte, du kan kontrollera den **/var/log/cloud-init.log** för mer information.  Sök efter följande post:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Nästa steg
Ytterligare cloud-init exempel av konfigurationsändringar finns i följande:
 
- [Lägg till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en pakethanterare för att uppdatera befintliga paket vid första start](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för virtuell dator](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)
