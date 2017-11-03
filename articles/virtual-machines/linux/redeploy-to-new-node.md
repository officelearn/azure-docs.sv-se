---
title: Distribuera virtuella Linux-datorer i Azure | Microsoft Docs
description: "Hur du distribuerar virtuella Linux-datorer i Azure för att minimera SSH-anslutningsproblem."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.openlocfilehash: 7bf69b2a3c006faa0dc0144313e5ebb64e941e2d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Distribuera virtuell Linux-dator till den nya noden i Azure
Om du står inför svårigheter felsökning SSH eller kan bidra till att programmet åtkomst till en Linux-dator (VM) i Azure, omdistribuera den virtuella datorn. När du distribuerar en virtuell dator, flyttar den virtuella datorn till en ny nod i Azure-infrastrukturen och sedan aktiveras den tillbaka. Alla konfigurationsalternativ och associerade resurser bevaras. Den här artikeln visar hur du distribuerar en virtuell dator med hjälp av Azure CLI eller Azure-portalen.

> [!NOTE]
> När du distribuerar en virtuell dator tillfälliga disken går förlorad och dynamiska IP-adresser som är kopplade till virtuella nätverksgränssnittet har uppdaterats. 

Du kan distribuera en virtuell dator med någon av följande alternativ. Du behöver bara välja ett alternativ för att distribuera om den virtuella datorn:

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure Portal](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Använda Azure CLI 2.0
Installera senaste [Azure CLI 2.0](/cli/azure/install-az-cli2) och logga in till en Azure med hjälp av [az inloggningen](/cli/azure/#login).

Distribuera den virtuella datorn med [az vm Omdistributionen](/cli/azure/vm#redeploy). I följande exempel återdistribuerar den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Använda Azure CLI 1.0
Installera den [senaste Azure CLI 1.0](../../cli-install-nodejs.md), logga in på ett Azure-konto och se till att du är i läget Resource Manager (`azure config mode arm`).

I följande exempel återdistribuerar den virtuella datorn med namnet *myVM* i resursgrupp med namnet *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Nästa steg
Om du har problem med anslutningen till den virtuella datorn kan du hittar detaljerad hjälp på [felsökning av SSH-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [detaljerad SSH felsökningssteg](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Om du inte kommer åt ett program som körs på den virtuella datorn, du kan också läsa [felsökning av problem med programmet](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

