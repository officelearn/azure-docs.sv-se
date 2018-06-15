---
title: Använda molnet init för att köra ett bash-skript i en Linux-VM i Azure | Microsoft Docs
description: Hur du använder molntjänster init för att köra ett bash-skript i en Linux-VM under skapande av med Azure CLI 2.0
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
ms.openlocfilehash: 471749563fae5b5de6e98e22ebf2ec5cc9365368
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
ms.locfileid: "29123726"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Använda molnet init för att köra ett bash-skript i en Linux VM i Azure
Den här artikeln visar hur du använder [moln init](https://cloudinit.readthedocs.io) för att köra en befintlig bash-skript på en Linux-dator (VM) eller virtuella datorn anger (VMSS) vid etablering tid i Azure. Skripten molnet init körs vid den första starten när resurserna som har etablerats genom Azure. Mer information om hur molnet init internt fungerar i Azure- och Linux-distributioner som stöds finns [moln init-översikt](using-cloud-init.md)

## <a name="run-a-bash-script-with-cloud-init"></a>Kör ett bash-skript med molnet initiering
Med molnet initiering du inte behöver konvertera din befintliga skript till ett moln-config accepterar molnet init flera indatatyper, av vilka en är ett bash-skript.

Om du har använt Azure tillägget för Linux anpassat skript för att köra skript, kan du migrera dem för att använda molnet init. Emellertid Azure Extensions har integrerat rapporterar till avisering till skriptfel, en moln-init avbildningsdistribution misslyckas om skriptet misslyckas.

Skapa ett enkelt bash-skript för att testa om du vill se den här funktionen i åtgärden. T.ex. moln-init `#cloud-config` filen skriptet måste vara lokal för där du ska köra kommandona AzureCLI att etablera den virtuella datorn.  I det här exemplet skapar du filen i molnet Shell inte på den lokala datorn. Du kan använda valfri redigerare som du vill. Ange `sensible-editor simple_bash.sh` för att skapa filen och visa en lista över tillgängliga redigeringsprogram. Välj #1 att använda den **nano** editor. Se till att hela molnet init-filen har kopierats korrekt, särskilt den första raden.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Innan du distribuerar den här avbildningen måste du skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#az_group_create) kommando. En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Nu ska du skapa en virtuell dator med [az vm skapa](/cli/azure/vm#az_vm_create) och ange bash-skriptfil med `--custom-data simple_bash.sh` på följande sätt:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Verifiera bash-skript har körts
SSH till den offentliga IP-adressen på den virtuella datorn visas i utdata från kommandot ovan. Ange en egen **publicIpAddress** på följande sätt:

```bash
ssh <publicIpAddress>
```

Ändra till den **/tmp** katalogen och kontrollera att myScript.txt-filen finns och har den aktuella texten inuti den.  Om inte, kan du kontrollera den **/var/log/cloud-init.log** för mer information.  Sök efter följande post:

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Nästa steg
Ytterligare molnet initiering exempel av konfigurationsändringar finns i följande:
 
- [Lägga till en ytterligare Linux-användare till en virtuell dator](cloudinit-add-user.md)
- [Kör en package manager för att uppdatera befintliga paket på första starten](cloudinit-update-vm.md)
- [Ändra lokala värdnamnet för VM](cloudinit-update-vm-hostname.md) 
- [Installera ett programpaket, uppdatera konfigurationsfiler och mata in nycklar](tutorial-automate-vm-deployment.md)