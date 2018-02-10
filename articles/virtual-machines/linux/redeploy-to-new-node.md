---
title: Distribuera virtuella Linux-datorer i Azure | Microsoft Docs
description: "Hur du distribuerar virtuella Linux-datorer i Azure för att minimera SSH-anslutningsproblem."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: iainfou
ms.openlocfilehash: 48b4e5f2429ce2bd8a875b084694f83e467b5575
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera virtuell Linux-dator till den nya noden i Azure
Om du står inför svårigheter felsökning SSH eller kan bidra till att programmet åtkomst till en Linux-dator (VM) i Azure, omdistribuera den virtuella datorn. När du distribuerar en virtuell dator, flyttar den virtuella datorn till en ny nod i Azure-infrastrukturen och sedan aktiveras den tillbaka. Alla konfigurationsalternativ och associerade resurser bevaras. Den här artikeln visar hur du distribuerar en virtuell dator med hjälp av Azure CLI eller Azure-portalen.

> [!NOTE]
> När du distribuerar en virtuell dator tillfälliga disken går förlorad och dynamiska IP-adresser som är kopplade till virtuella nätverksgränssnittet har uppdaterats. 

Du kan distribuera en virtuell dator med någon av följande alternativ. Du behöver bara välja ett alternativ för att distribuera om den virtuella datorn:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure-portalen](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Installera senaste [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till din Azure med hjälp av [az inloggningen](/cli/azure/#az_login).

Distribuera den virtuella datorn med [az vm Omdistributionen](/cli/azure/vm#az_vm_redeploy). I följande exempel återdistribuerar den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Använda Azure CLI 1.0
Installera den [senaste Azure CLI 1.0](../../cli-install-nodejs.md) och logga in på ditt Azure-konto. Kontrollera att du är i läget Resource Manager (`azure config mode arm`).

I följande exempel återdistribuerar den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn kan du hittar detaljerad hjälp på [felsökning av SSH-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [detaljerad SSH felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

